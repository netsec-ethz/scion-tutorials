---
title: SCION IP Gateway (SIG)
parent: Applications
nav_order: 60
---

# SCION IP Gateway (SIG)

The [SCION IP Gateway `SIG`](https://github.com/netsec-ethz/scion/tree/scionlab/go/sig) enables legacy IP applications to communicate over SCION. This tutorial describes how to set up two SIGs locally to test the SIG can enable any IP application to communicate over SCION (this can be extended to support multiple SIG enpoints).

## Environment

To test the SIG we will make use of the Vagrant configurations provided on [scionlab.org](https://scionlab.org/).
Set up a Vagrant VM on a host A and a host B using the instructions for [running inside a VM]({% link content/install/vm.md %}) and [creating an AS
 over VPN]({% link content/config/create_as.md %}).

First, the SIG binary must be built. Please refer to the `building from source` docs page: https://docs.scionlab.org/content/install/src.html for instructions on setting up the proper environment (in particular setting up the Go workspace and ensuring you have the correct go version).

Clone the SIG source files.
```shell
cd ~
git clone -b scionlab https://github.com/netsec-ethz/scion
# Needed go version is specified in go.mod
# The README discusses setting up your Go workspace, setting $GOPATH in .profile
```

## Configuring the two SIGs

We will now create the configuration for two SIG instances, one on the AS you started on host A `AS A` which we will call sigA and one on host B in `AS B`, which we will call sigB.
We will walk through creating the configuration directories for the SIGs, building the SIG binary and seting the linux capabilities on the binary:


```shell
# First, set some variables that will be used throughout this tutorial
export SC=/etc/scion
export LOG=/var/log/scion
export ISD=$(ls /etc/scion/gen/ | grep ISD | awk -F 'ISD' '{ print $2 }')
export AS=$(ls /etc/scion/gen/ISD${ISD}/ | grep AS | awk -F 'AS' '{ print $2 }')
export IA=${ISD}-${AS}
export IAd=$(echo $IA | sed 's/_/\:/g')


# Then, create a configuration directory for the SIG
sudo mkdir -p ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/

# Next, build the SIG binary
cd ~/scion/go/sig
go install
go build -o ${GOPATH}/bin/sig ~/scion/go/sig/main.go

# Finally, set the linux capabilities required by the SIG binary
sudo setcap cap_net_admin+eip ${GOPATH}/bin/sig
```

Additionally, the following need to be set to enable routing:

```shell
sudo sysctl net.ipv4.conf.default.rp_filter=0
sudo sysctl net.ipv4.conf.all.rp_filter=0
sudo sysctl net.ipv4.ip_forward=1
```

Create the configuration for the sig at ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.config:

*Note:* you need to replace ${SC}, ${ISD}, ${AS}, ${IA}, ${IAd}, ${sigID}, and ${sigIP} with the actual values on your system in these configuration files. You may define ${sigID} and ${sigIP}, for example ${sigID}="sigA" and ${sigIP}="172.16.0.11" for sigA and ${sigIP}="172.16.0.12" for sigB just ensure that the ${sigIP} value is different on sigA and sigB.

```
[features]
    # Feature flags are various boolean properties as defined in go/lib/env/features.go

[log]
    [log.file]
        # Location of the logging file. If not specified, logging to file is disabled.
        path = "${LOG}/sig${IA}-1.log"

        # File logging level. (trace|debug|info|warn|error|crit) (default debug)
        level = "debug"

        # Max size of log file in MiB. (default 50)
        size = 50

        # Max age of log file in days. (default 7)
        max_age = 7

        # Maximum number of log files to retain. (default 10)
        max_backups = 10

        # How frequently to flush to the log file, in seconds. If 0, all messages
        # are immediately flushed. If negative, messages are never flushed
        # automatically. (default 5)
        flush_interval = 5

    [log.console]
        # Console logging level (trace|debug|info|warn|error|crit) (default crit)
        level = "crit"

[metrics]
    # The address to export prometheus metrics on (host:port or ip:port or :port).
    # The prometheus metrics can be found under /metrics, furthermore pprof
    # endpoints are exposed see (https://golang.org/pkg/net/http/pprof/).
    # If not set, metrics are not exported. (default "")
    prometheus = ""

[sciond_connection]
    # Address of the SCIOND server the client should connect to.
    address = "127.0.0.1:30255"

    # Maximum time spent attempting to connect to sciond on start. (default 20s)
    initial_connect_period = "20s"

    # Maximum numer of paths provided by SCIOND.
    # Zero means that all the paths should be provided. (default 0)
    path_count = 0

[sig]
    # ID of the SIG. (required)
    id = "${sigID}"

    # The SIG config json file. (required)
    sig_config = "${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.json"

    # The local ISD-AS. (required)
    isd_as = "${IAd}"

    # The bind IP address. (required)
    ip = "${sigIP}"

    # Control data port, e.g. keepalives. (default 30256)
    ctrl_port = 30256

    # Encapsulation data port. (default 30056)
    encap_port = 30056

    # Name of TUN device to create. (default DefaultTunName)
    tun = "${sigID}"

    # Id of the routing table. (default 11)
    tun_routing_table_id = 11
```

You can do so with the following command:

```shell
sudo sed -i "s/\${IA}/${IA}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.config
sudo sed -i "s/\${IAd}/${IAd}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.config
sudo sed -i "s/\${AS}/${AS}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.config
sudo sed -i "s/\${ISD}/${ISD}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.config
sudo sed -i "s%\${SC}%${SC}%g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.config
sudo sed -i "s%\${LOG}%${LOG}%g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.config
sudo sed -i "s/\${sigID}/${sigID}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.config
sudo sed -i "s/\${sigIP}/${sigIP}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.config
```


Create the traffic rules for the sigs at ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.json:

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
You need to replace the "`<remote_sig_AS>`" AS id of the remote AS (for example, on sigA `<remote_sig_AS>` is the identifier of `AS B` in the format of "`${IAd}`"). Additionally, you need to replace "`<remote_sig_IPnet>`" with the subnet for traffic that will be routed on the sig. For this tutorial we are choosing to set this to `172.16.12.0/24` on `sigA.json` and `172.16.11.0/24` on `sigB.json`. Additional SIG end points can be accomodated by adding additional entries to this file.

Additionally, the topology files need to be updated to include the a sig entry. The following lines need to be added to each topology file (`${SC}/gen/ISD${ISD}/AS${AS}/endhost/topology.json`, `${SC}/gen/ISD${ISD}/AS${AS}/br${IA}-1/topology.json`, and `${SC}/gen/ISD${ISD}/AS${AS}/cs${IA}-1/topology.json`) above the line specifying the ISD_AS:
```
  "SIG": {
    "sig${IA}-1": {
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

For these changes in the topology files to take effect, the scionlab process needs to be restarted. 
```shell
sudo systemctl restart scionlab.target
```


Since we are running our SIGs in a VM and we do not have spare physical interfaces on which to run them, we will create two dummy interfaces:

```shell
sudo modprobe dummy
```

On host A: 
```shell
sudo ip link add dummy11 type dummy
# ${sigIP}="172.16.0.11" from sigA.config
sudo ip addr add ${sigIP}/32 brd + dev dummy11 label dummy11:0
```

On host B: 
```
sudo ip link add dummy12 type dummy
# ${sigIP}="172.16.0.12" from sigB.config
sudo ip addr add ${sigIP}/32 brd + dev dummy12 label dummy12:0
```

Now we need to add the routing rules for the two SIGs:

On host A:
```shell
# where <remote_sig_IPnet>=172.16.12.0/24 from sigA.json
sudo ip rule add to <remote_sig_IPnet> lookup 11 prio 11
```

On host B:
```shell
# where <remote_sig_IPnet>=172.16.11.0/24 from sigB.json
sudo ip rule add to <remote_sig_IPnet> lookup 11 prio 11
```

To show the ip rules and routes, run:
```shell
sudo ip rule show
sudo ip route show table 11
```

Now start the two SIGs with the following commands:


```shell
$GOPATH/bin/sig -config=${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/${sigID}.config &
```

## Testing

You can test that your SIG configuration works by running some traffic over it.

Add some client on host A and server on host B:

Host A (client):
```shell
sudo ip link add client type dummy
sudo ip addr add 172.16.11.1/24 brd + dev client label client:0
```

Host B (server):
```shell
sudo ip link add server type dummy
sudo ip addr add 172.16.12.1/24 brd + dev server label server:0
mkdir /tmp/WWW
touch /tmp/WWW/hello.html
echo "Hello World!" > /tmp/WWW/hello.html
cd /tmp/WWW/ && python3 -m http.server --bind 172.16.12.1 8081 &
```


Query the server running on host B from host A:

Host A:
```shell
curl --interface 172.16.11.1 172.16.12.1:8081/hello.html
```

You should see the "Hello World!" message as output from the last command.

