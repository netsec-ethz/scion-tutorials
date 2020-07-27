---
title: SCION IP Gateway (SIG)
parent: Applications
nav_order: 60
---

# SCION IP Gateway (SIG)

The [SCION IP Gateway `SIG`](https://github.com/netsec-ethz/scion/tree/scionlab/go/sig) enables legacy IP applications to communicate over SCION.
This tutorial describes a minimal setup of two SIGs, to test how the SIG can enable any IP application to communicate over SCION. This setup can later be extended to support multiple SIG enpoints.

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

## Configuring the two SIGs

We first create the configuration for two SIG instances, one in AS A, which we will call sigA and one in AS B, which we will call sigB.

The `scion-sig` package includes configuration file templates.
First, we copy and fill-in the `sig.toml` template:

```shell
# Set some variables that will be used below:
sigID=< sigA on host A, sigB on host B >
sigIP=127.0.0.1  # IP for the SCION address on which this SIG will bind
ISD=$(ls /etc/scion/gen/ | grep ISD | awk -F 'ISD' '{ print $2 }')
AS=$(ls /etc/scion/gen/ISD${ISD}/ | grep AS | awk -F 'AS' '{ print $2 }')

# Create a configuration directory for the SIG
sudo mkdir /etc/scion/gen/ISD${ISD}/AS${AS}/sig${ISD}-${AS}-1/

# Expand the placeholders in the sig.toml template and install it:
sed -e "s/\${ISD}/${ISD}/g;
        s/\${AS}/${AS}/g;
        s/\${IA}/${ISD}-${AS}/g;
        s/\${IAd}/${ISD}-${AS//_/:}/g;
        s/\${sigID}/${sigID}/g;
        s/\${sigIP}/${sigIP}/g;" < /usr/share/doc/scion-ip-gateway/templates/sig.config \
        | sudo tee --output-error=exit /etc/scion/gen/ISD${ISD}/AS${AS}/sig${ISD}-${AS}-1/sig.toml
```

Each SIG requires traffic rules, in the form of a `json` configuration file.
This configuration specifies IP prefixes that can be forwarded to a SIG in a remote AS.
Create the traffic rules for the SIG at `/etc/scion/gen/ISD${ISD}/AS${AS}/sig${ISD}-${AS}-1/${sigID}.json`:

```
{
    "ASes": {
        "<remote_sig_AS>": {
            "Nets": [
                "<remote_sig_IPnet>"
            ]
        }
    },
    "ConfigVersion": 9001
}
```

Here, we need to replace the "`<remote_sig_AS>`" with the ISD-AS number of the remote AS. For example, on sigA `<remote_sig_AS>` is the identifier of `AS B`, in the format `1-ff00:0:a12`. Additionally, you need to replace "`<remote_sig_IPnet>`" with the subnet for traffic that will be routed on the sig. For this tutorial we choose `172.16.12.0/24` in `sigA.json` and `172.16.11.0/24` in `sigB.json`. Additional SIG endpoints can be accomodated by adding additional entries to this file.

Finally, the topology files need to be updated to include a SIG entry. This entry is required so that the border routers can resolve the SIG service address. Insert the following snippet to the topology file of every border router in the AS:
```
  "SIG": {
    "sig${ISD}-${AS}-1": {
      "Addrs": {
        "IPv4": {
          "Public": {
            "Addr": "127.0.0.1",
            "L4Port": 31056
          }
        }
      }
    }
  },
```

For these changes in the topology files to take effect, the services need to be restarted, e.g. run
```shell
sudo systemctl restart scionlab.target
```

## Running the SIGs

Start the SIG process on both hosts, by executing:
```shell
sudo -u scion sig -config=/etc/scion/gen/ISD${ISD}/AS${AS}/sig${ISD}-${AS}-1/sig.toml
```

{% include alert type="Tip" content="
Now, you should already have connectivity between the SIGs. You can verify this, e.g. run `ping -I sigA 172.16.12.7` in host A,  and observe the arriving packets with `tcpdump -n -i sigB` on host B. However, there will not be replies to the pings yet.
" %}

## Configuring Routing (simple)

Now we setup the IP routing on both hosts, that we can _use_ the SIG connection.
There are a large number of possibilities to set this up; in this section we just describe a simple option without IP routing, where only applications on SIG hosts can make use of the link.

On host A:
```shell
# Assign an address in the range <remote_sig_IPnet> used in sigB.json
sudo ip address add 172.16.11.1 dev sigA
# Setup route to <remote_sig_IPnet> in sigA.json
sudo ip route add 172.16.12.0/24 dev sigA
```

On host B:
```shell
# Assign an address in the range <remote_sig_IPnet> used in sigA.json
sudo ip address add 172.16.12.1 dev sigB
# Setup route to <remote_sig_IPnet> in sigB.json
sudo ip route add 172.16.11.0/24 dev sigB
```

{% include alert type="Hint" content="
These address and route settings will only live as long as the sig tunnel device. As soon as the `sig` process terminates, this will be gone.
" %}

Now you should be able to `ping` the remote host, e.g. run on host A
```shell
ping 172.16.12.1
```

{% include alert type="Warning" content="
The MTU set on the SIG's tun device is unreliable or just plain wrong. Set a conservative value of e.g. 1200 bytes on both hosts:
```shell
sudo ip link set mtu 1200 dev ${sigID}
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
