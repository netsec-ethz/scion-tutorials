---
title: SCION IP Gateway (SIG)
parent: Applications
nav_order: 60
---

# SCION IP Gateway (SIG)

!!! Warning

    This page has not been updated after the latest changes to SCIONLab and is out of date.

The [SCION IP Gateway `SIG`](https://github.com/netsec-ethz/netsec-scion/tree/scionlab/go/sig) enables legacy IP applications to communicate over SCION. This tutorial describes how to set up two SIGs locally to test the SIG can enable any IP application to communicate over SCION.

## Environment

To test the SIG we will make use of the Vagrant configurations provided on [scionlab.org](https://scionlab.org/).
Set up a Vagrant VM on a host A and a host B using the instructions from [Virtual machine with VPN](https://netsec-ethz.github.io/scion-tutorials/virtual_machine_setup/dynamic_ip/).

## Configuring the two SIGs

We will now create the configuration for two SIG instances, one on the AS you started on host A `AS A` which we will call sigA and one on host B in `AS B`, which we will call sigB.
Create the configuration directories for the SIGs, build the SIG binary and set the linux capabilities on the binary:

```shell
export IA=$(cat $SC/gen/ia)
export IAd=$(cat $SC/gen/ia | sed 's/_/\:/g')
export AS=$(cat $SC/gen/ia | cut --fields=2 --delimiter="-")
export ISD=$(cat $SC/gen/ia | cut --fields=1 --delimiter="-")
mkdir -p ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/
go build -o ${SC}/bin/sig ${SC}/go/sig/main.go
sudo setcap cap_net_admin+eip ${SC}/bin/sig
```

Enable routing:

```shell
sudo sysctl net.ipv4.conf.default.rp_filter=0
sudo sysctl net.ipv4.conf.all.rp_filter=0
sudo sysctl net.ipv4.ip_forward=1
```

Create the configuration for the sigA at ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigA.config:

(You need to replace ${AS}, ${IA} and ${IAd} with the actual values on your system in these configuration files.)

```
[sig]
  # ID of the SIG (required)
  ID = "sigA"

  # The SIG config json file. (required)
  SIGConfig = "/home/ubuntu/go/src/github.com/scionproto/scion/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigA.json"

  # The local IA (required)
  IA = "${IAd}"

  # The bind IP address (required)
  IP = "10.0.8.A"

  # Control data port, e.g. keepalives. (default 10081)
  CtrlPort = 10081

  # Encapsulation data port. (default 10080)
  EncapPort = 10080

  # SCION dispatcher path. (default "")
  Dispatcher = ""

  # Name of TUN device to create. (default DefaultTunName)
  Tun = "sigA"

  # Id of the routing table (default 11)
  TunRTableId = 11

[sd_client]
  # Sciond path. It defaults to sciond.DefaultSCIONDPath.
  Path = "/run/shm/sciond/default.sock"

  # Maximum time spent attempting to connect to sciond on start. (default 20s)
  InitialConnectPeriod = "20s"

[logging]
[logging.file]
  # Location of the logging file.
  Path = "/home/ubuntu/go/src/github.com/scionproto/scion/logs/sig${IA}-1.log"

  # File logging level (trace|debug|info|warn|error|crit) (default debug)
  Level = "debug"

  # Max size of log file in MiB (default 50)
  # Size = 50

  # Max age of log file in days (default 7)
  # MaxAge = 7

  # How frequently to flush to the log file, in seconds. If 0, all messages
  # are immediately flushed. If negative, messages are never flushed
  # automatically. (default 5)
  FlushInterval = 5
[logging.console]
  # Console logging level (trace|debug|info|warn|error|crit) (default crit)
  Level = "debug"

[metrics]
# The address to export prometheus metrics on. (default 127.0.0.1:1281)
  Prometheus = "127.0.0.1:1282"
```

You can do so with the following command:

```shell
sed -i "s/\${IA}/${IA}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigA.config
sed -i "s/\${IAd}/${IAd}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigA.config
sed -i "s/\${AS}/${AS}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigA.config
sed -i "s/\${ISD}/${ISD}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigA.config
export tunIP=$(echo $(ip a | grep "global tun") | cut --fields=2 --delimiter=" " | cut --fields=1 --delimiter="/")
sed -i "s/10.0.8.A/${tunIP}/g" ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigA.config
```


Create the traffic rules for the sigA at ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigA.json:

```
{
    "ASes": {
        "17-ffaa:1:XXX": {
            "Nets": [
                "172.16.11.0/24"
            ],
            "Sigs": {
                "remote-1": {
                    "Addr": "10.0.8.XXX",
                    "CtrlPort": 10084,
                    "EncapPort": 10083
                }
            }
        }
    },
    "ConfigVersion": 9001
}
```
You need to replace the "10.0.8.XXX" IP with the actual IP of the remote host B and 17-ffaa:1:XXX with the AS id of the remote AS B.

And similarly the configuration and traffic rules for the sigB:

at ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigB.config

```
[sig]
  # ID of the SIG (required)
  ID = "sigB"

  # The SIG config json file. (required)
  SIGConfig = "/home/ubuntu/go/src/github.com/scionproto/scion/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigB.json"

  # The local IA (required)
  IA = "${IAd}"

  # The bind IP address (required)
  IP = "172.16.0.12"

  # Control data port, e.g. keepalives. (default 10081)
  CtrlPort = 10084

  # Encapsulation data port. (default 10080)
  EncapPort = 10083

  # SCION dispatcher path. (default "")
  Dispatcher = ""

  # Name of TUN device to create. (default DefaultTunName)
  Tun = "sigB"

  # Id of the routing table (default 11)
  TunRTableId = 12

[sd_client]
  # Sciond path. It defaults to sciond.DefaultSCIONDPath.
  Path = "/run/shm/sciond/default.sock"

  # Maximum time spent attempting to connect to sciond on start. (default 20s)
  InitialConnectPeriod = "20s"

[logging]
[logging.file]
  # Location of the logging file.
  Path = "/home/ubuntu/go/src/github.com/scionproto/scion/logs/sig${IA}-1.log"

  # File logging level (trace|debug|info|warn|error|crit) (default debug)
  Level = "debug"

  # Max size of log file in MiB (default 50)
  # Size = 50

  # Max age of log file in days (default 7)
  # MaxAge = 7

  # How frequently to flush to the log file, in seconds. If 0, all messages
  # are immediately flushed. If negative, messages are never flushed
  # automatically. (default 5)
  FlushInterval = 5
[logging.console]
  # Console logging level (trace|debug|info|warn|error|crit) (default crit)
  Level = "debug"

[metrics]
# The address to export prometheus metrics on. (default 127.0.0.1:1281)
  Prometheus = "127.0.0.1:1282"
```

and 

at ${SC}/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigB.json

```
{
    "ASes": {
        "17-ffaa:1:XXX": {
            "Nets": [
                "172.16.12.0/24"
            ],
            "Sigs": {
                "remote-1": {
                    "Addr": "10.0.8.XXX",
                    "CtrlPort": 10081,
                    "EncapPort": 10080
                }
            }
        }
    },
    "ConfigVersion": 9001
}
```
You need to replace the "10.0.8.XXX" IP with the actual IP of the remote host A and 17-ffaa:1:XXX with the AS id of the remote AS A.

Since we are running our SIGs in a VM and we do not have spare physical interfaces on which to run them, we will create two dummy interfaces:

```shell
sudo modprobe dummy
```

On host A:
```shell
sudo ip link set name dummy11 dev dummy0
sudo ip addr add 172.16.0.11/32 brd + dev dummy11 label dummy11:0
```

On host B:
```
sudo ip link add dummy12 type dummy
sudo ip addr add 172.16.0.12/32 brd + dev dummy12 label dummy12:0
```

Now we need to add the routing rules for the two SIGs:

On host A:
```shell
sudo ip rule add to 172.16.12.0/24 lookup 11 prio 11
```

On host B:
```shell
sudo ip rule add to 172.16.11.0/24 lookup 12 prio 12
```

Now start the two SIGs with the following commands:


On Host A:
```shell
$SC/bin/sig -config=/home/ubuntu/go/src/github.com/scionproto/scion/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigA.config > $SC/logs/sig${IA}-1.log 2>&1 &
```
and Host B:
```shell
$SC/bin/sig -config=/home/ubuntu/go/src/github.com/scionproto/scion/gen/ISD${ISD}/AS${AS}/sig${IA}-1/sigB.config > $SC/logs/sig${IA}-1.log 2>&1 &
```

To show the ip rules and routes, run:
```shell
sudo ip rule show
sudo ip route show table 11
sudo ip route show table 12
```

## Testing

You can test that your SIG configuration works by running some traffic over it.

Add some server on host A and client on host B:

Host A:
```shell
sudo ip link add server type dummy
sudo ip addr add 172.16.12.1/24 brd + dev server label server:0

mkdir $SC/WWW
echo "Hello World!" > $SC/WWW/hello.html
cd $SC/WWW/ && python3 -m http.server --bind 172.16.12.1 8081 &
```

Host B:
```shell
sudo ip link add client type dummy
sudo ip addr add 172.16.11.1/24 brd + dev client label client:0
```


Query the server running on host A from host B:

Host B:
```shell
curl --interface 172.16.11.1 172.16.12.1:8081/hello.html
```

You should see the "Hello World!" message as output from the last command.





