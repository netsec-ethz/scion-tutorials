# Running the SCION infrastructure on a local topology

## Introduction

This tutorial will guide you through the steps required to generate a local topology and run a SCION network locally on your system.

## Prerequisites

This tutorial assumes that SCION is already installed on your system. If this is not the case, please follow [How to build SCION on Ubuntu 16.04 x86-64](/native_setup/ubuntu_x86_build/) or [How to build SCION on Raspberry PI](/native_setup/rpi_ubuntu/).

## Generating topology

Before continuing with the following steps, you should first navigate to the SCION root directory:

```shell
cd $SC
```

The SCION installation comes with a command to generate the local topology from 'topo' configuration files. A default topology is defined in `topology/Default.topo` and it is depicted in the following figure:

![Default topology](/images/default_topology.png)

!!! warning "Creating a topology overwrites the existing installation"
    When running the topology command deletes the current topology, which is stored in the gen folder. We thus advise to back up the current gen directory **before** calling the topology creation command.

	In case the gen folder was accidentally overwritten, you need to re-establish it after running the local topology if you want to get back to your previous installation. In case of re-creating the SCIONLab topology running in a VM, re-establishing is easy:
	```shell
	cd $SC
	rm -rf gen
	cp -r /vagrant/gen .
	```

!!! tip "Reset runtime environment after topology changes"
    Every time a new topology is instantiated, the SCION runtime environment needs to be reset as described [here](/scion_tricks/changing_gen_dir/#restarting-scion-infrastructure).

To generate the default topology, you can run

```shell
./scion.sh topology
```

This command will generate the topology information in the `gen` directory. The structure of the `gen` directory will be:

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

!!! warning "Running large topology"
    Running the default SCION topology **requires significant amount of system resources**. This might not be possible on IoT devices like Raspberry PI or on a resource-limited virtual machine. For this reason, using a smaller topology is recommended. To generate a small topology with only a single ISD and 3 ASes, you can use the predefined definition of tiny topology as follows:

    ```
    ./scion.sh topology -c topology/Tiny.topo
    ```

Some notes about the topology definition in `topology/Default.topo`

* `defaults.subnet` (optional): override the default subnet of `127.0.0.0/8`.

* `core` (optional): specify if this is a core AS or not (defaults to 'false').

* `beacon_servers`, `certificate_servers`, `path_servers`, (all optional):
  number of such servers in a specific AS (override the default value 1).

* `links`: keys are `ISD_ID-AS_ID` (format also used for the keys of the JSON
  file itself) and values can either be `PARENT`, `CHILD`, `PEER`, or
  `CORE`.


### Running SCION

Running the SCION infrastructure:

```shell
./scion.sh run
```

Stopping the SCION infrastructure:

```shell
./scion.sh stop
```

Testing the infrastructure:

```shell
./scion.sh test
```

## Next steps

After running the SCION infrastructure, it is necessary to verify that it is running properly. This is covered in the tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation/).
