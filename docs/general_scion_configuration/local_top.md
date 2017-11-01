# Running SCION infrastructure on local topology 

## Introduction

This tutorial will guide you through steps required to generate local topology and run SCION. 

## Prerequisites

This tutorial assumes that SCION is already installed on your system. If this is not the case, please follow [How to build SCION on Ubuntu 16.04 x86-64](ubuntu_x86_build)

## Topology

Before continuing with following steps, you should first navigate to scion root directory:

```shell
cd $GOPATH/src/github.com/netsec-ethz/scion
```

SCION installation comes with command to generate default local topology. Topology is defined in directory `topology` and its depicted in following figure.

![Default topology](/images/default_topology.png)

To generate default topology it is sufficient to run

```shell
./scion.sh topology
```

This command will generate topology information in `gen` directory. Structure of gen directory will be:

```
 ./gen/ISD{X}/AS{Y}/
     {elem}{X}-{Y}-{Z}/
         as.yml
         path_policy.yml
         supervisord.conf
         topology.yml
         certs/
             ISD{X}-AS{Y}-V0.crt
             ISD{X}-V0.trc
         keys/
             as-sig.key
```

** This command will delete previous `gen` directory, if necessary back it up before! **

Note about topology definition in `topology/Default.topo`

* `defaults.subnet` (optional): override the default subnet of `127.0.0.0/8`.

* `core` (optional): specify if this is a core AS or not (defaults to 'false').

* `beacon_servers`, `certificate_servers`, `path_servers`, (all optional):
  number of such servers in a specific AS (override the default value 1).

* `links`: keys are `ISD_ID-AS_ID` (format also used for the keys of the JSON
  file itself) and values can either be `PARENT`, `CHILD`, `PEER`, or
  `CORE`.


### Running SCION

Running SCION infrastructure:

```shell
./scion.sh run
```

Stopping SCION infrastructure:

```shell
./scion.sh stop
```

Testing the infrastructure:

```shell
./scion.sh test
```

## Next steps

After running SCION infrastructure it is necessary to verify that its running properly. This is covered in tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation)