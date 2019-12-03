---
title: RAINS, Another Internet Naming Service
parent: Applications
nav_order: 70
---

# RAINS, Another Internet Naming Service

{% include alert type="Warning" content="This page has not been updated after the latest changes to SCIONLab and is out of date." %}

RAINS is an alternate protocol for Internet name resolution, designed as a replacement of the Domain Name System (DNS) and is used in SCIONLab.

## Setup

The following step assume that you are already running a SCION AS.
Install the tool rdig, which allows you to make RAINS queries over SCION:

```shell
go get github.com/netsec-ethz/rains/cmd/rdig
```

Display the rdig help to learn about the parameters:
```shell
rdig --help
```


Add some convenience functions to your shell to facilitate using rains with the SCION tools until rains support gets integrated natively:

```shell
echo 'mydig() { rdig @17-ffaa:0:1107,[192.33.93.195] "$@" scionip4 -p 5025 | grep ":A:" | awk "{print \$6}" | sed "s/:scionip4://"; }' >> ~/.profile
echo 'myHost() { echo `cat $SC/gen/ia | tr "_" ":"`,[127.0.0.1]; }' >> ~/.profile
source ~/.profile
```

## Making queries

To the resolver running in the Attachment Point:

```
rdig @17-ffaa:0:1107,[192.33.93.195] ns1.snet. scionip4 -p 5025
```

To a rainsd server for the zone node.snet. :

```
rdig @17-ffaa:0:1107,[192.33.93.195] ap17.node.snet. scionip4 -p 55553
```

## Using scmp with rains

```shell
$SC/bin/scmp echo -c 3 -local `myHost` -remote `mydig ap17.node.snet.`
```

