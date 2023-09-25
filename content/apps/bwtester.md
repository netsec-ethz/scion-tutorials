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
See [Installation](../install/pkg.html#applications) for details.

## bwtestclient

You can run the bandwidth test client application as follows:

```
scion-bwtestclient -s <server-address>
```

Sample servers are installed at the various locations in the SCIONLab network, [see below](#scionlab-bandwidth-test-servers).


### Controlling the bandwidth
The application supports specification of the test duration (up to 10 seconds), the packet size to be used (at least 4 bytes), the total number of packets that will be sent, and the target bandwidth. For instance, `5,100,10,1600bps` specifies that 10 packets of size 100 bytes will be sent over 5 seconds, resulting in a bandwidth of 1600bps. The question mark `?` character can be used as wildcard for any of these parameters. Its value is then computed according to the other parameters. The parameters for the test in the client-to-server direction are specified with `-cs`, and the server-to-client direction with `-sc`. So for instance to send 1 Mbps for 10 seconds from the client to the server, and 10 Mbps from the server to the client, you can use this command:

```
scion-bwtestclient -s <server-address> -cs 10,1000,1250,1Mbps -sc 10,1000,12500,10Mbps
```
For more information run the application without arguments to print its usage.

{% include alert type="Tip" content="
`bwtestclient` is also available via the [webapp](../apps/as_visualization/webapp_apps.html).
" %}

## bwtestserver

The server is started as follows:

```
scion-bwtestserver --listen=:30100
```

where `--listen` specifies the server (optional address and) port.


## SCIONLab Bandwidth Test Servers

There are multiple bandwidth servers deployed inside the SCIONLab infrastructure. Some of them can be reached using the following addresses

```
16-ffaa:0:1001,[172.31.0.23]:30100
16-ffaa:0:1002,[172.31.43.7]:30100
16-ffaa:0:1003,[172.31.19.144]:30100
16-ffaa:0:1004,[172.31.0.28]:30100
16-ffaa:0:1005,[172.31.26.94]:30100
16-ffaa:0:1007,[172.31.21.177]:30100
17-ffaa:0:1102,[129.132.121.164]:30100
17-ffaa:0:1102,[192.33.92.68]:30100
17-ffaa:0:1102,[192.33.93.177]:30100
17-ffaa:0:1107,[192.33.93.195]:30100
17-ffaa:0:1108,[195.176.0.237]:30100
17-ffaa:0:1108,[195.176.28.157]:30100
18-ffaa:0:1201,[128.2.24.126]:30100
19-ffaa:0:1303,[141.44.25.144]:30100
19-ffaa:0:1309,[158.42.255.13]:30100
20-ffaa:0:1401,[134.75.250.114]:30100
20-ffaa:0:1404,[203.230.60.98]:30100
26-ffaa:0:2001,[134.75.253.105]:30100
26-ffaa:0:2002,[134.75.253.20]:30100
```
