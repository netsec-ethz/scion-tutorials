---
title: Bandwidth Tester
parent: Applications
nav_order: 30
---

# Bandwidth Tester

The [bandwidth testing application `bwtester`](https://github.com/netsec-ethz/scion-apps/) enables a variety of bandwidth tests on the SCION network. Installation and usage are described below. Documentation of the code and protocol are described in the [bwtester README](https://github.com/netsec-ethz/scion-apps/blob/master/bwtester/README.md).

## Install

To install `bwtestclient` and `bwtestserver`, run:
```shell
sudo apt install scion-apps-bwtester
```
See [Installation](../install/pkg.md#applications) for details.

## bwtestclient

Sample servers are installed at the following locations:

* `17-ffaa:1:13,[192.168.1.79]:30100`
* `17-ffaa:1:f,[10.0.2.15]:30100`
* `19-ffaa:1:22,[141.44.25.146]:30100`

You can test the application as follows:

```
scion-bwtestclient -c 17-ffaa:1:89,[127.0.0.1]:0 -s 17-ffaa:1:13,[192.168.1.79]:30100
```

Here, `-s` specifies the bandwidth server whereas `-c` specifies your machine.
The `-c` flag can be omitted if a SCION localhost address is configured. To do this, add your
SCION host to the `/etc/hosts` file. Below you can see an example:

```
# regular IPv4 hosts
# ....

# regular IPv6 hosts
# ....

# SCION hosts
17-ffaa:0:1,[192.168.1.1]                               localhost
```

### Controlling the bandwidth
The application supports specification of the test duration (up to 10 seconds), the packet size to be used (at least 4 bytes), the total number of packets that will be sent, and the target bandwidth. For instance, `5,100,10,1600bps` specifies that 10 packets of size 100 bytes will be sent over 5 seconds, resulting in a bandwidth of 1600bps. The question mark `?` character can be used as wildcard for any of these parameters. Its value is then computed according to the other parameters. The parameters for the test in the client-to-server direction are specified with `-cs`, and the server-to-client direction with `-sc`. So for instance to send 1 Mbps for 10 seconds from the client to the server, and 10 Mbps from the server to the client, you can use this command:

```
scion-bwtestclient -s 17-ffaa:1:13,[192.168.1.79]:30100 -cs 10,1000,1250,1Mbps -sc 10,1000,12500,10Mbps
```
For more information run the application without arguments to print its usage.

{% include alert type="Tip" content="
`bwtestclient` is also available via the [webapp](../as_visualization/webapp_apps.md).
" %}

## bwtestserver

The server is started as follows:

```
scion-bwtestserver -s 17-ffaa:0:1102,[192.33.93.177]:30100 &
```

Here `-s` specifies your server address. Again, it can be omitted by specifying a SCION localhost like above. The server then
uses localhost to bind to. In this case, the port can be specified with the `-p` flag.


```
scion-bwtestserver -p 30100 &
```
