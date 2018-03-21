# Building SCION for a Raspberry Pi &ndash; Ubuntu MATE

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
sudo apt install git
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

### Step Two &ndash; apply necessary patches

On ARM architectures, it is necessary to apply two patches as followings:

```shell
git checkout -b arm-modified
wget https://gist.githubusercontent.com/FR4NK-W/cc6661f420fe5e9805d5b9cdb9c41b1b/raw/7dc3b60b86b4c148c2706e3da82eee8d557bbd45/patches_checksum_bench.patch
wget https://gist.githubusercontent.com/FR4NK-W/fb7a4b171ab3d5121b6492b9b664fd47/raw/ddeeb8f2337c64f027955e070df6ef34ff26bf9a/patches_dispatcher.patch

patch ./c/lib/scion/checksum_bench.c ./patches_checksum_bench.patch
rm c/lib/scion/checksum_bench.c.orig
rm patches_checksum_bench.patch
patch ./c/dispatcher/dispatcher.c ./patches_dispatcher.patch
rm c/dispatcher/dispatcher.c.orig
rm patches_dispatcher.patch
```

In order to enable updating the system, we commit the patched changes into the local arm-modified branch:

```shell
git commit -am "Modified to compile on ARM systems"
```

!!! warning "Troubleshooting"
    If your git identity is not configured, commits won't be possible. Configuring the user identity on a newly installed git can be done as follows:

```shell
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

### Step Three &ndash; configure Python path variable

Some SCION components like SCIONviz require Python libraries which are located in the scion root directory. In order to make them accessible, exporting the `PYTHONPATH` environment variable is required:

```shell
echo 'export PYTHONPATH="$SC/python:$SC"' >> ~/.profile
source ~/.profile
```

### Step Four &ndash; finish installing the required packages

In order to instal dependencies, simply issue the following command while in the root directory of the SCION installation:

```shell
bash -c 'yes | GO_INSTALL=true ./env/deps'
```

!!! note
    You might be asked to enter the sudo password after running the command

This will finish installing the required dependencies and system packages.

### Step Five &ndash; configure the host Zookeeper instance

Replacing `/etc/zookeeper/conf/zoo.cfg` with the file `docker/zoo.cfg` is recommended. This has the standard parameters set, as well as using a ram disk for the data log, which greatly improves the performance of Zookeeper (at the cost of reliability, so it should only be done in a testing environment).

```shell
cp docker/zoo.cfg /etc/zookeeper/conf/zoo.cfg
```

## Next steps

After finishing the installation of SCION, there are different ways of running different topologies. The following tutorials will cover this in further detail:

1. [Configure SCION to run on system boot](/scion_tricks/setup_startup.md) &ndash; Use systemd to run SCION and SCION-viz when system is started.
1. [Running a local network topology](/general_scion_configuration/local_top/) &ndash; Generate a sample topology and run SCION locally
1. [Connecting to SCIONLab with a static public IP address](/general_scion_configuration/public_ip/) &ndash; Connect to the already running SCION topology.
1. [Connecting to SCIONLab with a static public IP address, but behind a NAT](/general_scion_configuration/public_ip_nat/)
1. [Connecting to SCIONLab without a static public IP address](/general_scion_configuration/vpn_setup/) &ndash; Connect to the already running SCION topology through an OpenVPN tunnel.
