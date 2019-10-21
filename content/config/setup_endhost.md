# Set up an end host

## Introduction

In this tutorial we will cover the steps necessary to configure a SCION end host in a SCIONLab AS.

An SCION _end host_ is a simply a computer running SCION applications in a SCION AS, i.e. it is not a router and does not run any infrastructure services for the AS.
An end host will communicate with the Path- and Certificate-service of the AS to get paths and certificate information. For communication to hosts in different ASes, traffic will be sent to the SCION border routers of the AS.
The end host knows the addresses for these services from a configuration file (`topology.json`).

The software stack for a SCION end host application consists of the `dispatcher`, responsible for managing sockets and encapsulating/decapsulating SCION packets for IP/UDP overlay,
and the SCION-daemon `sciond`, which is responsible for fetching, verifying and caching paths and certificate information from the AS services.

Compared to the perhaps more familiar software stack for IP, we can see some rough analogues:

- `dispatcher`: corresponds to the kernels `socket` API
- `sciond`: similar to a local caching DNS resolver daemon (like e.g. dnsmasq, unbound), except it's for paths and certificates, not for names


## 1. Install SCION on end host

The software stack for a SCION end host consists of the `dispatcher` and the `sciond`, contained in the `scion-dispatcher` and `scion-daemon` packages.
Just install an end host application that you're planning to run.

On Ubuntu/Debian, run the following snippet to add the package repository:
```shell
sudo apt-get install apt-transport-https
echo "deb [trusted=yes] https://packages.netsec.inf.ethz.ch/debian all main" | sudo tee /etc/apt/sources.list.d/scionlab.list
sudo apt-get update
```
and then install the packages using:
```
sudo apt-get install scionlab scion-apps-* # Just get everything OR
sudo apt-get install scion-apps-bwtester   # get individual application(s), with only minimal dependencies
```

Of course you can also use the other [available installation options](../install/index.md).
When running the VM installation, the steps will be virtually identical with the only difference that they need to be performed in the respective VMs.
When running SCION built from sources, the directory paths will be different (configuration in `$GOPATH/src/github.com/scionproto/scion` instead of `/etc/scion`) and the `systemctl` commands would be replaced with the `scion.sh` script.


## 2. Modify AS configuration

The configuration downloaded from SCIONLab configures all SCION services to listen only on the localhost address by default.
To run an end host on a different host, the services need to bind on an IP that is accessible from the end host.

On the host running the AS services, locate the `topology.json` files in `/etc/scion/gen/`. In this configuration file, we change the occurrences of IP `127.0.0.1`
to the hosts IP.
```
NODE_IP=#..host IP..# sed -i "s/127\.0\.0\.1/$NODE_IP/" /etc/scion/gen/ISD*/AS*/*/topology.json
```

!!! Note
    Only `PathServer`, `CertificateServer` and the `InternalAddrs` of `BorderRouter` _need_ to be accessible for the end host.
    This command also changes the address for the `BeaconServer` and the `CtrlAddr` of the `BorderRouter` as a bonus, for the sake of simplicity.

Restart your AS services by running `sudo systemctl restart scionlab.target`.


## 3. Extract and adapt end host configuration

To create the configuration for the end host, we copy the configuration (modified in the previous step) from the node's `/etc/scion/gen` directory: we'll need the `dispatcher/` and the `ISD*/AS*/end host` directory.

The following snippet describes one way to achieve this:

```shell
cp /etc/scion/gen /tmp/
rm -r /tmp/gen/ISD*/AS*/{br,bs,ps,cs}*/  # strip config for AS services
```

Then we need to fix another configuration file:
the localhost addresses in `/etc/scion/gen/ISD*/AS*/end host/sd.toml` must be changed to to the end host's IP address.
```
ENDHOST_IP=#..host IP..# sed -i "s/127\.0\.0\.1/$ENDHOST_IP/" /etc/scion/gen/ISD*/AS*/end host/sd.toml
```

Now copy `/tmp/gen` to the end host (e.g. using `scp`) and install it in the `/etc/scion/` directory.


## 4. Start SCION on end host

Finally we can start the `scion-dispatcher` and `scion-daemon` services:

```shell
# replace XX and YYYY with your ISD/AS number e.g. scion-daemon@17-ffaa_1_15b.service
systemctl enable scion-daemon@XX-ffaa_1_YYYY.service
systemctl start scionlab.target
```

Test that your connection is working, e.g. by using [`scmp echo`](../config/check.md#ping) and start using the applications as described in the Applications-section.
