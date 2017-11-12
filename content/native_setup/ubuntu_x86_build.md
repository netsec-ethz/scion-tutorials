# Building SCION on Ubuntu 16.04 x86-64


## Introduction

This tutorial will guide you through steps required to install SCION on Ubuntu 16.04. This also includes steps required to install necessary packages. For details on how to build SCION on RaspberryPI please take a look at [How to build SCION for Raspberry PI](rpi_raspbian.md)

## Prerequisites

Following steps will guide you through installation of required tools necessary for running SCION.

!!! tip
    You can skip prerequisites if you have Go workspaces already configured on your machine.

### Step One - install Go

In order to run SCION you must have Go version 1.8.x installed. Download [Go tools](https://golang.org/doc/install?download=go1.8.5.linux-amd64.tar.gz "Go binary for x86-64") and extract them in `/usr/local` with following command (with root permissions):

```shell
tar -C /usr/local -xzf go1.8.5.linux-amd64.tar.gz
```

and add newly extracted binaries to PATH:

```shell
echo 'PATH=$PATH:/usr/local/go/bin' >> ~/.profile
source ~/.profile
```

Test your Go installation with following command:

```shell
go version
```

And output should be: `go version go1.8.5 linux/amd64`

### Step Two - configuring Go workspace

After installation of Go tools, it is necessary to setup [Go workspace](https://golang.org/doc/code.html#GOPATH "Go workspace"). Following commands will create default workspace at `~/go` and export it as `$GOPATH` environment variable.

```shell
echo 'export GOPATH="$HOME/go"' >> ~/.profile
source ~/.profile
mkdir -p "$GOPATH/bin"
echo 'PATH=$PATH:$GOPATH/bin' >> ~/.profile
source ~/.profile
```

## SCION Installation

### Step One - Checkout repository

After go workspace has been configured, we can checkout SCION repository with all dependencies using following command:

```shell
mkdir -p "$GOPATH/src/github.com/netsec-ethz"
cd "$GOPATH/src/github.com/netsec-ethz"
git clone --recursive -b scionlab git@github.com:netsec-ethz/scion
```

!!! warning "Troubleshooting"
    If the machine doesn't have generated ssh keys or ssh keys are not assigned to github account, checkout will fail with following error: `Permission denied (publickey)`. There are two ways to resolve this problem:

    1. Changing checkout using ssh to https: 
    ```
    git config --global url.https://github.com/.insteadOf git@github.com:
    ```
    2. Assign SSH keys to Github account, detailed instruction can be found on [Github help](https:/    /help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)


This will clone SCION and resolve dependencies in appropriate directory in Go workspace. It is necessary to navigate to the newly downloaded repository for finishing the configuration.

```shell
cd $GOPATH/src/github.com/netsec-ethz/scion
```

### Step Two - Finish installing required packages

Installing dependencies is fairly simple, while in root of `scion` repository it is necessary to execute following command:

```shell
./env/deps
```

This will finish installing required dependencies and system packages.

### Step Three - Configure host Zookeeper instance

Replacing `/etc/zookeeper/conf/zoo.cfg` with file `docker/zoo.cfg` is recommended. This has the standard parameters set, as well as using a ram disk for the data log, which greatly improves ZK performance (at the cost of reliability, so it should only be done in a testing environment). 

```shell
cp docker/zoo.cfg /etc/zookeeper/conf/zoo.cfg
```

## Next steps

After finishing installation of SCION there are different ways of running different topologies. Following tutorials will cover this in details.

1. [Running topology locally](/general_scion_configuration/local_top) - Generate sample topology and run SCION
2. [Running topology on SCION Lab with public IP address](#) - Connects to already running SCION topology 
2. [Running topology on SCION Lab without public IP address](#) - Connects to already running SCION topology through OpenVPN tunnel.