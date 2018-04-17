# Building SCION on Ubuntu 16.04 x86-64

## Introduction

This tutorial will guide you through the steps required to install SCION on an x86 system running Ubuntu 16.04. For details on how to build SCION on a Raspberry Pi, please take a look at [how to build SCION on a Raspberry Pi](rpi_ubuntu.md).

## Easy Way (using the SCION install script)

The easy way to install SCION is to use the SCION install script:

```shell
wget https://raw.githubusercontent.com/netsec-ethz/scion-coord/master/scion_install_script.sh
chmod +x scion_install_script.sh
```

The SCION install script supports various ways to connect to SCION:

### Running the SCION infrastructure on a local topology

If the SCION install script is executed without any arguments, SCION will be run on a local topology:

```shell
./scion_install_script.sh
```

For more information, check out [Running the SCION infrastructure on a local topology](/general_scion_configuration/local_top/).

### Connecting to SCIONLab

Installing a SCION AS and connecting it to SCIONLab requires that you have already downloaded the necessary configuration from the [SCION Coordination Service](https://www.scionlab.org/). To do so, please follow one of the following options, depending on the network configuration of the system on which SCION will be installed:

1. [Connecting to SCIONLab with a static public IP address](/general_scion_configuration/public_ip/)
1. [Connecting to SCIONLab with a static IP address, but behind a NAT](/general_scion_configuration/public_ip_nat/)
1. [Connecting to SCIONLab via VPN (without a static IP address)](/general_scion_configuration/vpn_setup/)

The configuration downloaded from the [SCION Coordination Service](https://www.scionlab.org/) includes a `gen` folder that needs to be uploaded to the target system and provided as an argument to the install script:

```shell
export BIDIR=`pwd`
./scion_install_script.sh -g $BIDIR/gen/
```

In case of connecting to SCIONLab via VPN, the OpenVPN client configurationi (`client.conf`), that is included in the configuration downloaded from the [SCION Coordination Service](https://www.scionlab.org/) needs to be provided as argument additionally:

```shell
export BIDIR=`pwd`
./scion_install_script.sh -g $BIDIR/gen/ -v $BIDIR/client.conf
```

### Next Steps

After running the SCION infrastructure, it is necessary to verify that it is running properly. This is covered in the tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation/).

## Advanced Way (manual installation)

The following steps will guide you through the manual installation of the tools necessary for running SCION. 

### Step One &ndash; install Go

In order to run SCION, you must have Go version 1.9.x installed. The installation steps below will automatically install the correct GO version, but be aware that if you are running a different Go version or are using a different gopath, the following steps may break other Go software that you are running.

### Step Two &ndash; configure your Go workspace

!!! tip
    You can skip this step if you already have a Go workspace configured on your machine.

It is necessary to set up your [Go workspace](https://golang.org/doc/code.html#GOPATH "Go workspace"). The following commands will create a default workspace at `~/go` and export it as the `$GOPATH` environment variable:

```shell
echo 'export GOPATH="$HOME/go"' >> ~/.profile
source ~/.profile
mkdir -p "$GOPATH/bin"
echo 'PATH=$PATH:$GOPATH/bin' >> ~/.profile
source ~/.profile
```

## SCION installation

### Step One &ndash; clone the SCION repository

After the Go workspace has been configured, we can check out the SCION repository from github.com with all dependencies using the following commands:

```shell
mkdir -p "$GOPATH/src/github.com/scionproto/scion"
cd "$GOPATH/src/github.com/scionproto/scion"
git clone --recursive -b scionlab git@github.com:netsec-ethz/netsec-scion .
```

!!! warning "Troubleshooting"
    If your account does not have an SSH key and that SSH key is not assigned to the github account, the checkout will fail with the error `Permission denied (publickey)`. There are two ways to resolve this problem:

    1. Changing the checkout using https instead of ssh:
    ```
    git config --global url.https://github.com/.insteadOf git@github.com:
    ```
    2. Assign an SSH key to your Github account, detailed instructions can be found on [Github help](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/).

This will clone the appropriate SCION directory into your Go workspace. We will create an environment variable `SC` that will point to the SCION root directory. Afterwards it is necessary to navigate to the newly downloaded repository for finishing the configuration:

```shell
echo 'export SC="$GOPATH/src/github.com/scionproto/scion"' >> ~/.profile
source ~/.profile
cd $SC
```

### Step Two &ndash; configure Python path variable

Some SCION components like SCIONviz require Python libraries which are located in the SCION root directory. In order to make them accessible, the `PYTHONPATH` environment variable needs to be exported:

```shell
echo 'export PYTHONPATH="$SC/python:$SC"' >> ~/.profile
source ~/.profile
```

### Step Three &ndash; finish installing the required packages

In order to install all the dependencies, simply issue the following command while in the root directory of the SCION installation:

```shell
bash -c 'yes | GO_INSTALL=true ./env/deps'
```

!!! note
    You might be asked for your sudo password after running the command

This will finish installing the required dependencies and system packages.

### Step Four &ndash; configure the host Zookeeper instance

Replacing `/etc/zookeeper/conf/zoo.cfg` with the file `docker/zoo.cfg` is recommended. This has the standard parameters set, as well as using a ram disk for the data log, which greatly improves the performance of Zookeeper (at the cost of reliability, so it should only be done in a testing environment).

```shell
cp docker/zoo.cfg /etc/zookeeper/conf/zoo.cfg
```

## Next steps

After finishing the installation of SCION, you can run the architecture on several different topologies. The following tutorials will cover this in further detail:

1. [Configure SCION to run on system boot](/scion_tricks/setup_startup.md) &ndash; Use systemd to run SCION and SCION-viz when system is started.
1. [Running a local network topology](/general_scion_configuration/local_top/) &ndash; Generate a sample topology and run SCION locally
1. [Connecting to SCIONLab with a static public IP address](/general_scion_configuration/public_ip/) &ndash; Connect to the already running SCION topology.
1. [Connecting to SCIONLab with a static public IP address, but behind a NAT](/general_scion_configuration/public_ip_nat/)
1. [Connecting to SCIONLab without a static public IP address](/general_scion_configuration/vpn_setup/) &ndash; Connect to the already running SCION topology through an OpenVPN tunnel.
