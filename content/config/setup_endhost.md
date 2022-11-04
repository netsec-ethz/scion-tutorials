---
title: Configuring a SCION end host
parent: Configuration
nav_order: 30
---

# Configuring a SCION end host

{% include alert type="note" content="
You should go through this tutorial if you want to configure a SCION end host, i.e. a SCION enabled host that does not run any infrastructure services or routers.
Before proceeding you should already have SCION AS services available in your network.
" %}

## Introduction

In this tutorial we will cover the steps necessary to configure a SCION end host in a SCIONLab AS.

A SCION end host is a machine running SCION applications in a SCION AS, i.e. it is not a router and does not run any infrastructure services for the AS.
An end host will communicate with the control service of the AS to get paths and certificate information. For communication to hosts in different ASes, traffic will be sent to the SCION border routers of the AS.
The end host knows the addresses for these services from a configuration file (`topology.json`).

The software stack for a SCION end host application consists of the `dispatcher`, responsible for managing sockets and encapsulating/decapsulating SCION packets for IP/UDP overlay,
and the SCION-daemon `sciond`, which is responsible for fetching, verifying and caching paths and certificate information from the AS services.

Compared to the perhaps more familiar software stack for IP, we can see some rough analogues:

- `dispatcher`: corresponds to the kernels IP and UDP stacks; it dispatches incoming packets based on address/port to the correct application socket, and it handles SCMP messages e.g, by replying to echo requests.
- `sciond`: similar to a local caching DNS resolver daemon (like e.g. dnsmasq, unbound), except it's for paths and certificates, not for names

## 1. Install SCION on the end host

The software stack for a SCION end host consists of the `dispatcher` and the `sciond`, contained in the `scion-dispatcher` and `scion-daemon` packages.

On Debian-based OS run the following snippet to add the package repository:
```shell
sudo apt-get install apt-transport-https ca-certificates
echo "deb [trusted=yes] https://packages.netsec.inf.ethz.ch/debian all main" | sudo tee /etc/apt/sources.list.d/scionlab.list
sudo apt-get update
```
and then install the packages using:
```
sudo apt-get install scion-daemon scion-dispatcher
```

Of course you can also use the other [available installation options](../install/index.html).
When running the VM installation, the steps will be virtually identical with the only difference that they need to be performed in the respective VMs.
When running SCION built from sources, the directory paths will be different (configuration in `gen/AS...` directory of the scion git worktree instead of `/etc/scion`) and the `systemctl` commands would be replaced with the `scion.sh` script.


## 2. Modify AS configuration

The configuration downloaded from SCIONLab configures all SCION services to listen only on the localhost address by default.
To run an end host on a different host, the services need to bind on an IP that is accessible from the end host.

Edit the file `/etc/scion/topology.json` on the host running the AS services, In this configuration file, we change the occurrences of IP `127.0.0.1` to the hosts IP.

```
export NODE_IP=example
sed -i "s/127\.0\.0\.1/$NODE_IP/" /etc/scion/topology.json
```

Restart your AS services by running

```
sudo systemctl restart scionlab.target
```

## 3. Copy end host configuration

To create the configuration for the end host, we copy the relevant parts of the configuration (modified in the previous step) from the node's `/etc/scion/` directory: we'll need the `topology.json` file and the `certs/` sub-directory.

The following example snippet uses `scp` to copy these files to the target machine:
```shell
cd /etc/scion/
scp -r topology.json certs/ <target-hostname>:/etc/scion/
```


## 4. Start SCION on end host

Finally we can start the `scion-dispatcher` and `scion-daemon` services:

```shell
systemctl enable --now scion-dispatcher.service
systemctl enable --now scion-daemon.service
```

Test that your connection is working, e.g. by using `scion ping` (as described in [checking AS configuration](../config/check.html#ping)) and start using the applications.
