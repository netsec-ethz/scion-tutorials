---
title: Setup FABRID for a SCIONLab AS
parent: Configuration
nav_order: 30
---

# Setup FABRID for a SCIONLab AS

## Introduction

This page describes the necessary steps to enable FABRID on a SCIONLab AS. Further information on the protocols 
implementation and policy definitions can be found in the [FABRID design document](https://github.com/netsec-ethz/scion/blob/scionlab/doc/dev/design/FABRID.rst).

## 1. Create a SCIONLab AS


Follow the [instructions](create_as.html) to create a SCIONLab AS and install it using one of the [available methods](../install/index.html).
Make sure the setup is working, e.g. by pinging another AS.


## 2. Enable FABRID

To allow FABRID to be used by end hosts in your AS and remote ASes, the configuration for the control service,
border router, and SCION daemon has to be updated. 
Modify the respective configuration files in the location corresponding to your installation method, e.g. `/etc/scion` for package installations.

### Control service
Edit `cs-1.toml` and add the following lines.

```toml
[fabrid]
enabled = true
path = "/etc/scion/fabrid-policies"

[drkey.level1_db]
connection = "/var/lib/scion/cs-1.drkey-level1.db"

[drkey.secret_value_db]
connection = "/var/lib/scion/cs-1.drkey-secret.db"

[drkey.delegation]
FABRID = [ "127.0.0.1",]
```
{% include alert type="note" content="
If you used a different installation method, replace the paths `/etc/scion` and `/var/lib/scion` with the corresponding locations.
" %}
{% include alert type="note" content="
If you have multiple border routers or don't use the default addresses, make sure to include all their IPs in `drkey.delegation`.
The IP address of your BRs can be found in `topology.json:"border_routers"/"br-X"/"internal_addr"`
" %}

### Border router
Edit `br-1.toml` (and additional border routers if any) and add the following lines.

```toml
[router]
drkey = [ "FABRID",]
fabrid = true
```

### SCION daemon
Edit `sciond.toml` and add the following lines.

```toml
[drkey_level2_db]
connection = "/var/lib/scion/sd.drkey_level2.db"
```

{% include alert type="note" content="
If you used a different installation method, replace the path `/var/lib/scion` with the corresponding location.
" %}

### Restart your AS services
Apply your configuration changes by running

```shell
sudo systemctl restart scionlab.target
```

## 3. Test your setup

To test FABRID using the `ping` command, append the flag `--fabridquery 0-0#0,0@0` which applies the default ZERO policy to all hops with FABRID enabled.
The output should contain:
```
Using selected FABRID policies:
    [18-ffaa:1:1151 ~ZERO~ 1>168 18-ffaa:0:1206  1>8 18-ffaa:0:1201  4>5 17-ffaa:0:1101 ]
```
with the `~ZERO~` policy being enabled for your AS.

## 4. (Optional) Define custom policies

Create a new directory on the path defined in the control service config above, i.e. `/etc/scion/fabrid-policies`.
For each distinct policy, add a new `yml` file to the folder.
In this example we define a local policy which can be used for outgoing connections over the interface 1.

Example policy `1-local.yml`:
```yml
connections:
    - ingress:
          type: wildcard
      egress:
          type: interface
          interface: 1
      mpls_label: 42
local: true
local_identifier: 1001
local_description: Fabrid Example Policy
```

After saving the file, restart the control service
```shell
sudo systemctl restart scionlab.target
```

You can now use the policy by specifying it in the `fabridquery`
```shell
scion ping 18-ffaa:1:1151,127.0.0.1 --fabridquery 0-0#0,0@L1001
```
