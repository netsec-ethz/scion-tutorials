---
title: RAINS, Another Internet Naming Service
parent: Applications
nav_order: 70
---

# RAINS, Another Internet Naming Service

{% include alert type="Warning" content="The RAINS infrastructure in SCIONLab is currently NOT available" %}

RAINS is an alternate protocol for Internet name resolution, designed as a replacement of the Domain Name System (DNS).
The current implementation of RAINS can run on SCION, and serve SCION addresses.

All of our apps can make use of RAINS to resolve hostnames, if a resolver is configured.

If you want to run your own RAINS servers, please refer to the documentation in the [READMEs](https://github.com/netsec-ethz/rains).


## Resolver config file

Create a file `/etc/scion/rains.cfg` and add a line with the address of a RAINS resolver. For example, to use the RAINS resolver in the ETHZ AS in SCIONLab (**which is currently not available**), set this to:

```
17-ffaa:0:1107,[192.33.93.195]:5025
```

## rdig

RAINS contains a commandline tool to peek at names, similar to the well known `dig` tool for DNS.

### Setup
Build from sources, requires an installation of go (~1.13).

```shell
go get github.com/netsec-ethz/rains/cmd/rdig
```

Display the rdig help to learn about the parameters:
```shell
rdig --help
```


### Making queries

Prerequisite is a running SCION end host stack.

To query a name on a RAINS resolver or name server at a known address/port, run:

```
rdig @<ISD-AS,IP> <name> scion -p <port>
```

For example, to send the a query for the name `ap17.node.snet` to the RAINS resolver configured above, run:

```
rdig @17-ffaa:0:1107,[192.33.93.195] ap17.node.snet. scion -p 5025
```
