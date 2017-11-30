# Building SCION on Ubuntu 16.04 x86-64


## Introduction

This tutorial will guide you through the steps required to install SCION on Ubuntu 16.04. This also includes the steps required to install the necessary packages. For details on how to build SCION on a Raspberry Pi, please take a look at [how to build SCION on a Raspberry Pi](rpi_ubuntu.md).

## Prerequisites

The following steps will guide you through the installation of the tools necessary for running SCION.

### Step One &ndash; install Go

In order to run SCION, you must have Go version 1.8.x installed. Installation will automatically install right GO version, but that might break other Go software that you are running.

### Step Two &ndash; configure your Go workspace

!!! tip
    You can skip this step if you have Go workspaces already configured on your machine.

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

After the Go workspace has been configured, we can checkout the SCION repository from github.com with all dependencies using the following commands:

```shell
mkdir -p "$GOPATH/src/github.com/netsec-ethz"
cd "$GOPATH/src/github.com/netsec-ethz"
git clone --recursive -b scionlab git@github.com:netsec-ethz/scion
```

!!! warning "Troubleshooting"
    If the machine doesn't have generated SSH keys or the SSH keys are not assigned to the github account, the checkout will fail with the error `Permission denied (publickey)`. There are two ways to resolve this problem:

    1. Changing the checkout using ssh to https:
    ```
    git config --global url.https://github.com/.insteadOf git@github.com:
    ```
    2. Assign SSH keys to Github account, detailed instruction can be found on [Github help](https:/    /help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

This will clone SCION appropriate directory in the Go workspace. We will create export environment variable `SP` that will point to SCION root directory. Afterwards it is necessary to navigate to the newly downloaded repository for finishing the configuration:

```shell
echo 'export SC="$GOPATH/src/github.com/netsec-ethz/scion"' >> ~/.profile
source ~/.profile
cd $SC
```

### Step Two &ndash; configure python path variable

Some SCION components like SCIONviz require Python libraries which are located in scion root directory. In order to make them accessible, exporting `PYTHONPATH` environment variable is required:

```shell
echo 'export PYTHONPATH="$SC/python:$SC"' >> ~/.profile
source ~/.profile
```

### Step Three &ndash; finish installing the required packages

In order to instal dependencies, simply issue the following command while in the root directory of the SCION installation:

```shell
bash -c 'yes | GO_INSTALL=true ./env/deps'
```

!!! note
    You might be asked for sudo password after running the command

This will finish installing the required dependencies and system packages.

### Step Four &ndash; configure the host Zookeeper instance

Replacing `/etc/zookeeper/conf/zoo.cfg` with the file `docker/zoo.cfg` is recommended. This has the standard parameters set, as well as using a ram disk for the data log, which greatly improves the performance of Zookeeper (at the cost of reliability, so it should only be done in a testing environment).

```shell
cp docker/zoo.cfg /etc/zookeeper/conf/zoo.cfg
```

## Next steps

After finishing the installation of SCION, there are different ways of running different topologies. The following tutorials will cover this in further detail:

1. [Running a local network topology](/general_scion_configuration/local_top) &ndash; Generate a sample topology and run SCION locally
2. [Connecting to SCIONLab with a static public IP address](/general_scion_configuration/public_ip) &ndash; Connect to the already running SCION topology
2. [Connecting to SCIONLab without a static public IP address](/general_scion_configuration/vpn_setup) &ndash; Connect to the already running SCION topology through an OpenVPN tunnel
