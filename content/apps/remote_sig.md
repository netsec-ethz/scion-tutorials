---
title: SCION IP Gateway (SIG)
parent: Applications
nav_order: 60
---

# SCION IP Gateway (SIG)

The [SCION IP Gateway `SIG`](https://github.com/netsec-ethz/scion/tree/scionlab/go/posix-gateway) enables legacy IP applications to communicate over SCION.

## Install

To install the SIG, run:
```shell
sudo apt install scion-sig
```
See [Installation](../install/pkg.html#applications) for details.


## Tutorial: Set up a SIG tunnel between two ASes
The SIG forwards IP packets from the IP network in one SCION AS, across the SCION network, to the SIG in a remote SCION AS that contains the destination IP address.
From the IP perspective, the tunnel between two SIGs looks like any other IP link. The SIGs at either end of the tunnel act as IP routers.
This tunneling of IP traffic is intended to replace the BGP-based inter-domain IP routing by SCION, without requiring changes to legacy IP hosts and applications.

Each SIG establishes a session to one or multiple remote SIGs.
They can use statically configured IP routes or configure the routes dynamically using by announcing IP subnets to each other.

In this tutorial, we will create a very basic setup with two SIGs to route between two statically configured private IP networks.
We choose the private IP range `172.16.11.0/24` for AS A and `172.16.12.0/24` for AS B.


### Prerequisites
You will need two running SCION ASes.
Connectivity between these ASes needs to be working, but it does not otherwise matter how the ASes are set up.

The description below assumes that you're running the "standard" SCIONLab setup with all of the services and routers running on one separate machine for each of your ASes.
We'll refer to these two ASes as "AS A" and "AS B", and the hosts running them as "host A" and "host B", respectively.

Check that you have basic connectivity between your ASes, e.g. by running `scion showpaths <other AS>` and checking that paths are "alive".
If this is not the case, please go back to troubleshoot connectivity first.

### Fake address
The SIG listens on an address that needs to be in the its assigned IP subnet.
Normally, the SIG will be running on a host with this address on one of its network interfaces.
We fake this by adding the relevant address to the loopback interface.
```shell
sudo ip address add 172.16.11.1 dev lo  # on host A, 172.16.12.1 on host B
```

{% include alert type="Hint" content="
This is not persistent between reboots. Make sure to add this address before starting the SIG. If there is no interface with matching address, the SIG may fail to add routing table entries.
" %}

### SIG address configuration
This address also needs to be configured both in two configuration files:
Add the lines for `ctrl_addr` and `data_addr` to the `[gateway]` section in `/etc/scion/sig.toml`:
```toml
[gateway]
ctrl_addr = "172.16.11.1:30256"   # on host A, 172.16.12.1 on host B
data_addr = "172.16.11.1:30056"   #   ditto
```

Edit `/etc/scion/topology.json` and set define the same addresses in the `sigs` section:
```json
  "sigs": {
    "sig-1": {
      "ctrl_addr": "172.16.11.1:30256",  # on host A, 172.16.12.1 on host B
      "data_addr": "172.16.11.1:30056"   #   ditto
    }
  }
```
And finally, restart the SCION services after editing the `topology.json` file:

```shell
sudo systemctl restart scionlab.target
```

### SIG traffic rules

Each SIG requires traffic rules specified in the form of a `json` configuration file.
This configuration specifies IP prefixes that should be forwarded to SIGs in remote ASes.

A skeleton traffic rule configuration file is installed with the `scion-sig` package in `/etc/scion/sig.json`.
The skeleton file contains one dummy entry with placeholders "`<remote_sig_AS>`" and "`<remote_sig_IPnet>`".
The `<remote_sig_AS>` needs to be replaced with an AS ID in the form `1-ffaa:1:abc` and `<remote_sig_IPnet>` needs to specify IP networks in CIDR notation.
Note that multiple remote SIG endpoints can be specified, but in this example we have just one.

On host A, we have the `/etc/scion/sig.json` traffic rules:
```json
{
    "ASes": {
        "2-ffaa:0:b": { # put your AS B here
            "Nets": [
                "172.16.12.0/24"
            ]
        }
    },
    "ConfigVersion": 9001
}
```

On host B:
```json
{
    "ASes": {
        "1-ffaa:0:a": { # put your AS A here
            "Nets": [
                "172.16.11.0/24"
            ]
        }
    },
    "ConfigVersion": 9001
}
```

### Startup

Start the SIG systemd service on both hosts, by executing:
```shell
sudo systemctl start scion-ip-gateway.service
```

Running `ip link list`, you can now see the SIG's `tun` device.

Wait a few seconds after starting the SIG, as session establishment is not immediate.
In the meantime, you can inspect the status page that the SIG serves on a local diagnostics HTTP endpoint:
```shell
curl -sfS localhost:30456/status
```
Once the session is established, this page shows the current path and routing table information.

Running `ip route show`, you should now see the routing table entry for the remote IP subnet going over the `sig` tunnel device.

{% include alert type="Warning" content="
The MTU set on the SIG's tun device is unreliable or just plain wrong. Set a conservative value of e.g. 1200 bytes on both hosts:
```shell
sudo ip link set mtu 1200 dev sig
```
" %}


### Traffic!

Now, ping the remote IP address to check the connectivity via the SIGs:

```shell
ping 172.16.12.1  # on host A, 172.16.11.1 on host B
```
This ping should successfully show echo replies. These IP packets are sent via the `sig` tun interfaces and are transported over SCION.
Inspect the metrics of the SIG and find e.g. the number of the packets transported:
```shell
curl -sfS localhost:30456/metrics | grep gateway_ippkts_
```

We can also run some actual traffic over the SIG tunnel! Run a webserver on host A:
```shell
mkdir /tmp/www
echo "Hello World!" > /tmp/www/hello
cd /tmp/www/ && python3 -m http.server --bind 172.16.11.1
```

And query the web server running on host A from host B:
```shell
curl 172.16.11.1:8000/hello
```

You should see the "Hello World!" message as output from the last command.


{% include alert type="Hint" content="
The SIG appears to perform poorly (abnormally high rate or reordered and
dropped packets and consequently low throughput with TCP) when running in a
SCIONLab User AS with a provider link using OpenVPN. The exact cause is unclear,
likely there is some bad interplay between the two `tun` devices.
" %}


### Wrapping up

We've set up statically routed tunnel for legacy IP traffic between two SCION ASes using the SCION-IP-Gateway.
We've sent some traffic with legacy IP applications and seen that the SIGs tunnel the IP packets from one SIG to another.

What you could do next:
* Observe the traffic flowing in and out of the SIG tunnel with `sudo tcpdump -i sig`
* Add a third SIG, in a third AS, with a third IP subnet
* Setup an actual network that you want to route with the SIG instead of the faked address on the loopback interface.
  Enable IP forwarding on the SIG host in order to make it act as an IP router.


## Additional References

* Built-in help on `sig.toml` configuration: `scion-ip-gateway sample config`
* [SIG Reference Manual](https://scion.docs.anapaya.net/en/latest/manuals/gateway.html)
* [SIG Framing Protocol Specification](https://scion.docs.anapaya.net/en/latest/protocols/sig.html)
