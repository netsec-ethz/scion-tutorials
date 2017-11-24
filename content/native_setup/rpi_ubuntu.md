# Building SCION for a Raspberry Pi &ndash; Raspbian

## Introduction

The SCION infrastructure can also be run on IoT devices like a Raspberry Pi. Building SCION for a Raspberry Pi is similar to the [regular x86 build](ubuntu_x86_build), although there are a few additional steps required to make everything work.

## Prerequisites

In this tutorial, we assume that you already have a Raspberry Pi running Ubuntu MATE (or similar Ubuntu Xenial based distribution). In order to install Ubuntu MATE, please follow the [installation guide](https://ubuntu-mate.org/raspberry-pi/).

!!! note "Update packages to latest version"
    It is recommended to update all packages before starting the installation process of SCION:

    ```
    sudo apt update && sudo apt upgrade
    ```

## Install necessary tools

### Install necessary packages

```shell
sudo apt install git openvpn
```

### Configure Go workspace

!!! tip
    You can skip this step if you have Go workspaces already configured.

It is necessary to set up your [Go workspace](https://golang.org/doc/code.html#GOPATH "Go workspace"). The following commands will create a default workspace at `~/go` and export it as the `$GOPATH` environment variable:

```shell
echo 'export GOPATH="$HOME/go"' >> ~/.profile
source ~/.profile
mkdir -p "$GOPATH/bin"
echo 'PATH=$PATH:$GOPATH/bin' >> ~/.profile
source ~/.profile
```

## Install SCION

### Step One &ndash; clone the SCION repository

After the Go workspace has been configured, we can checkout the SCION repository from github.com with all dependencies using the following commands:

```shell
mkdir -p "$GOPATH/src/github.com/netsec-ethz"
cd "$GOPATH/src/github.com/netsec-ethz"
git clone --recursive -b scionlab git@github.com:netsec-ethz/scion
cd scion
```

!!! warning "Troubleshooting"
    If the machine doesn't have generated SSH keys or the SSH keys are not assigned to the github account, the checkout will fail with the error `Permission denied (publickey)`. There are two ways to resolve this problem:

    1. Changing the checkout using ssh to https:
    ```
    git config --global url.https://github.com/.insteadOf git@github.com:
    ```
    2. Assign SSH keys to Github account, detailed instruction can be found on [Github help](https:/    /help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

On ARM architecture it is necessary to apply two patches in following way:

```shell
git checkout -b arm-modified
wget https://gist.githubusercontent.com/FR4NK-W/cc6661f420fe5e9805d5b9cdb9c41b1b/raw/7dc3b60b86b4c148c2706e3da82eee8d557bbd45/patches_checksum_bench.patch
wget https://gist.githubusercontent.com/FR4NK-W/fb7a4b171ab3d5121b6492b9b664fd47/raw/ddeeb8f2337c64f027955e070df6ef34ff26bf9a/patches_dispatcher.patch

patch ./c/lib/scion/checksum_bench.c ./patches_checksum_bench.patch
rm c/lib/scion/checksum_bench.c.orig
patch ./c/dispatcher/dispatcher.c ./patches_dispatcher.patch
rm c/dispatcher/dispatcher.c.orig
```

In order to make it easier to track, we can commit patched changes:

```shell
git commit -am "Modified to compile on ARM systems"
bash -c 'yes | GO_INSTALL=true ./env/deps'
```

### Step Two &ndash; finish installing the required packages

In order to instal dependencies, simply issue the following command while in the root directory of the SCION installation:

```shell
bash -c 'yes | GO_INSTALL=true ./env/deps'
```

!!! note
    You might be asked for sudo password after running the command

This will finish installing the required dependencies and system packages.

### Step Three &ndash; configure the host Zookeeper instance

Replacing `/etc/zookeeper/conf/zoo.cfg` with the file `docker/zoo.cfg` is recommended. This has the standard parameters set, as well as using a ram disk for the data log, which greatly improves the performance of Zookeeper (at the cost of reliability, so it should only be done in a testing environment).

```shell
cp docker/zoo.cfg /etc/zookeeper/conf/zoo.cfg
```

## Next steps

After finishing the installation of SCION, there are different ways of running different topologies. The following tutorials will cover this in further detail:

1. [Running a local network topology](/general_scion_configuration/local_top) &ndash; Generate a sample topology and run SCION locally
2. [Connecting to SCIONLab with a static public IP address](/general_scion_configuration/public_ip) &ndash; Connect to the already running SCION topology
2. [Connecting to SCIONLab without a static public IP address](/general_scion_configuration/vpn_setup) &ndash; Connect to the already running SCION topology through an OpenVPN tunnel
