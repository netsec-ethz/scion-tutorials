# Set up SCINO endhost and connect to local AS

## Introduction

In this tutorial we will cover steps necessary to configure SCION endhost that will connect to already running SCION AS. 
This is useful in situations where you don't want to run complete AS infrastructure, but rather forward traffic to AS.

Depending on how SCION AS is run, steps in configuring endhost will slightly differ.

### Running AS in SCION VM

First way is running SCION AS as part of SCION virtual machine (VM), following figure depicts such a scenario.

![SCION AS in virtual machine](/images/vm_endhost_setup.png)

Installation steps are covered in following tutorials:

- [Running SCION VM with dynamic IP](/virtual_machine_setup/dynamic_ip.md)
- [Running SCION VM with static IP](/virtual_machine_setup/static_ip.md)

### Running AS natively

Second way is running SCION AS natively on a host machine, following figure depicts such a scenario.

![SCION running natively](/images/native_endhost_setup.png)

Installation steps can be found in following tutorial pages:

- [Installing SCION on Ubuntu 16.04 x86 machine](/native_setup/ubuntu_x86_build/)
- [Installing SCION on Ubuntu MATE 16.04 - Raspberry PI](/native_setup/rpi_ubuntu/)

## Prerequisites

For the sake of simplicity, we will assume that Host's IP address is 10.42.0.1 and endhost's IP address is 10.42.0.180 in rest of the tutorial. It is necessary for you to find correct IP addresses of for your devices and replace them accordingly in following steps.

If you are running SCION VM as your AS, you will need to install `iptable` utilities on your **endhost** device by running following command:

```shell
sudo apt install netfilter-persistent iptables-persistent
```

## Step One - Installing SCION on endhost

Any platform that runs SCION can be used as an endhost. To install SCION on different platforms you can follow one of the tutorials:

* [Installing SCION on Ubuntu 16.04 x86 machine](/native_setup/ubuntu_x86_build.md)
* [Installing SCION on Ubuntu MATE 16.04 - Raspberry PI](/native_setup/rpi_ubuntu.md)

Other SCION VM can also be configured to be used as endhost.

## Step Two - Copy initial configuration

After SCION infrastructure is successfully installed on your endhost device, we can start the configuration process. First of all we need to stop currently running scion topology and remove old `gen` directory.

```shell
cd $SC
./scion.sh stop
rm -rf gen
```

Next step is to make sure both endhost and SCION VM share the same configuration i.e. same `gen` directory. This can be done in several ways, easiest is copying it directly from AS system. 

Executing following command from **SCION AS** should be enough to copy complete `gen` directory to endhost:

```shell
scp -r /home/as_user/go/src/github.com/netsec-ethz/scion/gen endhost_user@10.42.0.180:/home/endhost_user/go/src/github.com/netsec-ethz/scion/gen
```

!!! Warning
    You will need to replace **endhost_user** with appropriate user on endhost, **as_user** with appropriate user from SCION AS and IP address 10.42.0.180 with actual IP address of the endhost device.

## Step Three - Remove unnecessary services

Next step is to disable unnecessary services, like border router, beacon server etc. on endhost device. This can be done by editing configuration file on **endhost's system**:

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
    **This step is only necessary if you are running AS in SCION virtual machine** if this is not the case, proceed to step five.

Configuration files we copied from VM in first step contain address `10.0.2.15`. This address is not accessible outside VM and we need to rewrite it to host's IP so packets get routed in right way. This can be done with iptables.

```shell
sudo iptables -t nat -A OUTPUT -m udp -p udp -d 10.0.2.15 -j DNAT --to-destination 10.42.0.1

sudo netfilter-persistent save
```

## Step Five - Restart SCION

Last step is to reload configuration and restart SCION on your endhost system.

```shell
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```

## Next steps

Best way to verify endhost configuration is by running properly is by running some demo applications:

* [Fetching sensor readings or time stamps](/sample_projects/fetch_sensor_readings.md)
* [Fetching a camera image over the SCION network](/sample_projects/access_camera.md)
* [Running the bandwidthtester application](/sample_projects/bwtester.md)