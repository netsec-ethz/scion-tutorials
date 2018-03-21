# Set up SCION endhost and connect to local AS infrastructure

## Introduction

In this tutorial we will cover the steps necessary to configure a SCION endhost that will connect to an already running SCION AS. 
This is useful in situations where your host can take advantage of an existing local SCION infrastructure.

Depending on how the SCION AS is set up, the steps for configuring the endhost will slightly differ.

### Running AS infrastructure executes in a VM

In the first way, the SCION AS runs inside a virtual machine (VM). The following figure depicts this scenario.

![SCION AS in virtual machine](/images/vm_endhost_setup.png)

The installation steps of the AS are covered in the following tutorials:

- [Running SCION VM with dynamic IP](/virtual_machine_setup/dynamic_ip.md)
- [Running SCION VM with static IP](/virtual_machine_setup/static_ip.md)

### Running AS infrastructure natively on a system

In the second way, the SCION AS is executing natively on a host machine. The following figure depicts this scenario.

![SCION running natively](/images/native_endhost_setup.png)

The installation steps for this setup is described in the following tutorial pages:

- [Installing SCION on Ubuntu 16.04 x86 machine](/native_setup/ubuntu_x86_build/)
- [Installing SCION on Ubuntu MATE 16.04 - Raspberry PI](/native_setup/rpi_ubuntu/)
- [Configuring AS and connecting to SCION network for devices with public static IP](/general_scion_configuration/public_ip/)
- [Configuring AS and connecting to SCION network for devices with public static IP behind a NAT](/general_scion_configuration/public_ip_nat/)
- [Configuring AS and connecting to SCION network using OpenVPN](/general_scion_configuration/vpn_setup/)

## Prerequisites

Throughout this setup we will use host and endhost IP addresses on both machines. In order to make everything easier to follow it is necessary to create two environment variables `HOST_IP` and `ENDHOST_IP` with respective addresses on **both machines** as they will be used throughout this setup. Execute following commands replacing correct IP addresses with correct ones:

```shell
export HOST_IP="10.42.0.1"
export ENDHOST_IP="10.42.0.180"
``` 

## Step One - Installing SCION on endhost

Any platform that runs SCION can be used as an endhost. To install SCION on different platforms you can follow one of the tutorials:

* [Installing SCION on Ubuntu 16.04 x86 machine](/native_setup/ubuntu_x86_build.md)
* [Installing SCION on Ubuntu MATE 16.04 - Raspberry PI](/native_setup/rpi_ubuntu.md)

Also, SCION VMs can be configured to be used as endhost.

## Step Two - Copy initial configuration

After the SCION environment is successfully installed on your endhost device, we can start the configuration process. First of all, we need to stop the currently running SCION environment and remove the old `gen` directory.

```shell
cd $SC
./scion.sh stop
rm -rf gen
```

The next step is to make sure both endhost and SCION AS share the same AS configuration, i.e., the same `gen` directory. This can be done in several ways, but the easiest is to copy it directly from the AS system. 

Executing the following command from **SCION AS** copies the complete `gen` directory to endhost. Note that you will need to replace **endhost_user** with appropriate user name on the endhost.

```shell
scp -r ${SC}/gen endhost_user@${ENDHOST_IP}:/home/endhost_user/go/src/github.com/scionproto/scion/gen
```

## Step Three - Remove unnecessary services

The next step is to disable unnecessary SCION services, like the border router, beacon server, etc., on the endhost device. This can be done by editing configuration file on the **endhost's system**:

```
vim $SC/gen/ISD{ISD_NUMBER}/AS{AS_NUMBER}/supervisord.conf
```

It is sufficient to remove last 2 lines that look similar to this:

```
[group:as1-1029]
programs = br1-1029-1,bs1-1029-1,cs1-1029-1,ps1-1029-1,sd1-1029
```

In the same file edit following line:

```
command = bash -c 'exec bin/sciond "--api-addr" "/run/shm/sciond/....
```

By adding additional `addr` argument to look like this:

```
command = bash -c 'exec bin/sciond "--addr" "10.42.0.180" "--api-addr" "/run/shm/sciond/
```

**Make sure you replace `10.42.0.180` to correct endhost's IP address.**

Next we need to remove all directories except `endhost` from `$SC/gen/ISD{ISD_NUMBER}/AS{AS_NUMBER}/` directory. 

```shell
cd $SC/gen/ISD{ISD_NUMBER}/AS{AS_NUMBER}
rm -rf *-*
```

## Step Four - Iptable rules

!!! warning
    **This step is only necessary if you are running the AS SCION infrastructure inside a Virtual Machine**. If this is not the case, proceed to step five.

Configuration files we copied from VM in first step contain address `10.0.2.15`. This address is not accessible outside the VM and we need to rewrite it to the host's IP address, so that packets get routed correctly. This can be done with iptables.

```shell
sudo apt install netfilter-persistent iptables-persistent

sudo iptables -t nat -A OUTPUT -m udp -p udp -d 10.0.2.15 -j DNAT --to-destination ${HOST_IP}

sudo netfilter-persistent save
```

## Step Five - Restart SCION

Last step is to reload configuration and restart SCION on your endhost system.

```shell
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```

## Next steps

The best way to verify endhost configuration is by running properly is by running some demo applications:

* [Fetching sensor readings or time stamps](/sample_projects/fetch_sensor_readings.md)
* [Fetching a camera image over the SCION network](/sample_projects/access_camera.md)
* [Running the bandwidthtester application](/sample_projects/bwtester.md)
