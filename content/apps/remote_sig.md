---
title: SCION IP Gateway (SIG)
parent: Applications
nav_order: 60
---

# SCION IP Gateway (SIG)

The [SCION IP Gateway `SIG`](https://github.com/netsec-ethz/scion/tree/scionlab/go/sig) enables legacy IP applications to communicate over SCION.
This tutorial describes a minimal setup of two SIGs, to test how the SIG can enable any IP application to communicate over SCION. This setup can later be extended to support multiple SIG endpoints.

## Install

To install `sig`, run:
```shell
sudo apt install scion-sig
```
See [Installation](../install/pkg.html#applications) for details.


## Prerequisites

You will need two running SCION ASes.

The description below assumes that you're running the "standard" SCIONLab setup with all of the services and routers running on one separate machine for each of your ASes.
We'll refer to these two ASes as "AS A" and "AS B", and the hosts running them as "host A" and "host B", respectively.

## Configuring SIG traffic rules

Each SIG requires traffic rules, in the form of a `json` configuration file.
This configuration specifies IP prefixes that should be forwarded to SIGs in remote ASes.


A skeleton traffic rule configuration file is installed with the `scion-sig` package in `/etc/scion/sig.json`.
The skeleton file contains one dummy entry with placeholders "`<remote_sig_AS>`" and "`<remote_sig_IPnet>`".
The `<remote_sig_AS>` needs to be replaced with an AS ID in the form `1-ffaa:1:abc` and `<remote_sig_IPnet>` needs to specify IP networks in CIDR notation.
Note that multiple remote SIG endpoints can be specified, but in this example we have just one.

We will now create the traffic rules on both host A and host B.
For this guide, we choose the private IP range `172.16.11.0/24` for AS A and `172.16.12.0/24` for AS B.

Thus, on **host A**, we have the `/etc/scion/sig.json` traffic rules:
```
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

On **host B**:
```
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


Start the SIG process on both hosts, by executing:
```shell
sudo -u scion scion-ip-gateway --config=/etc/scion/sig.toml
```

{% include alert type="Tip" content="
Now, you should already have connectivity between the SIGs. You can verify this, e.g. run `ping -I sig 172.16.12.7` in host A, and observe the arriving packets with `tcpdump -n -i sig` on host B. However, there will not be replies to the pings yet.
" %}

{% include alert type="Hint" content="
Apart from the traffic rules `sig.json` file, there is a `toml` configuration file where things like log levels, connection to sciond, and the name of the tunnel interface can be defined.
The default `/etc/scion/sig.toml` installed with the `scion-sig` package should work in most cases.
Run `scion-ip-gateway sample config` for information about the available configuration options.
" %}

## Configuring Routing

If you run `ip route`, you will observe that the SIG does not automatically configure any routes.
In order for the SIG link to be useful, we need to setup the networks in both ASes such that the relevant traffic will be routed over the `sig` interface.
There are a large number of possibilities to set this up; in this section we just describe a simple option without IP routing, so that only applications on the SIG hosts can make use of the link.

On host A:
```shell
# Assign an address in the address range chosen for AS A:
sudo ip address add 172.16.11.1 dev sig
# Setup route for <remote_sig_IPnet> in sig.json:
sudo ip route add 172.16.12.0/24 dev sig
```

On host B:
```shell
# Assign an address in the address range chosen for AS B:
sudo ip address add 172.16.12.1 dev sig
# Setup route for <remote_sig_IPnet> in sig.json:
sudo ip route add 172.16.11.0/24 dev sig
```

{% include alert type="Hint" content="
These address and route settings will only live as long as the `sig` tunnel device. As soon as the `scion-ip-gateway` process terminates, this will be gone.
" %}

Now you should be able to `ping` the remote host, e.g. run on host A
```shell
ping 172.16.12.1
```

{% include alert type="Warning" content="
The MTU set on the SIG's tun device is unreliable or just plain wrong. Set a conservative value of e.g. 1200 bytes on both hosts:
```shell
sudo ip link set mtu 1200 dev sig
```
" %}

## Testing

You can test that your SIG configuration works by running some traffic over it.

Add some client on host A and server on host B:

Host A (server):
```shell
mkdir /tmp/www
echo "Hello World!" > /tmp/www/hello
cd /tmp/www/ && python3 -m http.server --bind 172.16.11.1
```

Query the web server running on host A from host B:

Host B (client):
```shell
curl 172.16.11.1:8000/hello
```

You should see the "Hello World!" message as output from the last command.
