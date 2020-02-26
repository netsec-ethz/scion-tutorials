---
title: Configuring SCION end host
parent: Configuration
nav_order: 30
---

# Configuring SCION end host

{% include alert type="note" content="
You should go through this tutorial if you want to install SCION end host but do not want to run other SCION AS services on that machine.
Before proceeding you should already have SCION AS services available in your network, otherwise end host will not run properly.
" %}

## Introduction

In this tutorial we will cover the steps necessary to configure a SCION end host in a SCIONLab AS.

A SCION end host is a machine running SCION applications in a SCION AS, i.e. it is not a router and does not run any infrastructure services for the AS.
An end host will communicate with the path and certificate servers of the AS to get paths and certificate information. For communication to hosts in different ASes, traffic will be sent to the SCION border routers of the AS.
The end host knows the addresses for these services from a configuration file (`topology.json`).

The software stack for a SCION end host application consists of the `dispatcher`, responsible for managing sockets and encapsulating/decapsulating SCION packets for IP/UDP overlay,
and the SCION-daemon `sciond`, which is responsible for fetching, verifying and caching paths and certificate information from the AS services.

Compared to the perhaps more familiar software stack for IP, we can see some rough analogues:

- `dispatcher`: corresponds to the kernels `socket` API
- `sciond`: similar to a local caching DNS resolver daemon (like e.g. dnsmasq, unbound), except it's for paths and certificates, not for names

## 1. Install SCION on end host

The software stack for a SCION end host consists of the `dispatcher` and the `sciond`, contained in the `scion-dispatcher` and `scion-daemon` packages.
Just install an end host application that you're planning to run.

On Debian-based OS run the following snippet to add the package repository:
```shell
sudo apt-get install apt-transport-https
echo "deb [trusted=yes] https://packages.netsec.inf.ethz.ch/debian all main" | sudo tee /etc/apt/sources.list.d/scionlab.list
sudo apt-get update
```
and then install the packages using:
```
sudo apt-get install scion-daemon scion-dispatcher
```

Of course you can also use the other [available installation options](../install/index.html).
When running the VM installation, the steps will be virtually identical with the only difference that they need to be performed in the respective VMs.
When running SCION built from sources, the directory paths will be different (configuration in `$GOPATH/src/github.com/scionproto/scion` instead of `/etc/scion`) and the `systemctl` commands would be replaced with the `scion.sh` script.


## 2. Modify AS configuration

The configuration downloaded from SCIONLab configures all SCION services to listen only on the localhost address by default.
To run an end host on a different host, the services need to bind on an IP that is accessible from the end host.

On the host running the AS services, locate the `topology.json` files in `/etc/scion/gen/`. In this configuration file, we change the occurrences of IP `127.0.0.1`
to the hosts IP. In the service configuration toml files, we also replace the QUIC bind IP and omit the Prometheus metrics IP address, if we do not want to expose them.

```
export NODE_IP=example
sed -i "s/127\.0\.0\.1/$NODE_IP/" /etc/scion/gen/ISD*/AS*/*/topology.json
sed -i "s/Address = \"\[127\.0\.0\.1\]/Address = \"\[$NODE_IP\]/" /etc/scion/gen/ISD*/AS*/*/*.toml
```

{% include alert type="note" content="
Only `PathServer`, `CertificateServer` and the `InternalAddrs` of `BorderRouter` need to be accessible for the end host.
This command also changes the address for the `BeaconServer` and the `CtrlAddr` of the `BorderRouter` as a bonus, for the sake of simplicity.
" %}

Restart your AS services by running

```
sudo systemctl restart scionlab.target
```

## 3. Extract and adapt end host configuration

To create the configuration for the end host, we copy the configuration (modified in the previous step) from the node's `/etc/scion/gen` directory: we'll need the `dispatcher/` and the `ISD*/AS*/end host` directory.

The following snippet removes configuration of the SCION services from directory, keeping only the required part

```shell
cp /etc/scion/gen /tmp/
rm -r /tmp/gen/ISD*/AS*/{br,bs,ps,cs}*/
```

The Configuration of the `scion-daemon` also needs to be changed in order to use the IP address of the end host

```
export ENDHOST_IP=example
sed -i "s/127\.0\.0\.1/$ENDHOST_IP/" /tmp/gen/ISD*/AS*/endhost/sd.toml
```

At this moment `/tmp/gen` contains a full configuration needed for the SCION end host. You can use e.g. `scp` to install it in the `/etc/scion/` directory on the target machine.


## 4. Start SCION on end host

Finally we can start the `scion-dispatcher` and `scion-daemon` services:

```shell
# replace XX and YYYY with your ISD/AS number e.g. scion-daemon@17-ffaa_1_15b.service
systemctl enable --now scion-dispatcher.service
systemctl enable --now scion-daemon@XX-ffaa_1_YYYY.service
```

Test that your connection is working, e.g. by using `scmp echo` (as described in [checking AS configuration](../config/check.html#ping)) and start using the applications.
