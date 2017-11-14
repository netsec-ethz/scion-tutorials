# Building SCION for a Raspberry Pi &ndash; Raspbian

## Introduction

The SCION infrastructure can also be run on IoT devices like a Raspberry Pi. Building SCION for a Raspberry Pi is similar to the [regular x86 build](ubuntu_x86_build), although there are a few additional steps required to make everything work.

## Prerequisites

In this tutorial, we assume that you already have a Raspberry Pi running Raspbian. In order to install Raspbian, please follow the [installation guide](https://www.raspberrypi.org/downloads/raspbian/).

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

### Install Go

In order to run SCION, Go version `1.8.x` is required. You can download and install go with following commands:

```shell
wget https://storage.googleapis.com/golang/go1.8.5.linux-armv6l.tar.gz
sudo tar -C /usr/local -xzf go1.8.5.linux-armv6l.tar.gz

echo 'PATH=$PATH:/usr/local/go/bin' >> ~/.profile
source ~/.profile
```

### Configure Go workspace

After the installation of Go tools, it is necessary to create a go workspace with the following commands:

```shell
echo 'export GOPATH="$HOME/go"' >> ~/.profile
source ~/.profile
mkdir -p "$GOPATH/bin"
echo 'PATH=$PATH:$GOPATH/bin' >> ~/.profile
source ~/.profile
```

## Install SCION

```shell
mkdir -p "$GOPATH/src/github.com/netsec-ethz"
cd "$GOPATH/src/github.com/netsec-ethz"

git clone --recursive -b scionlab git@github.com:netsec-ethz/scion
cd scion

```

!!! warning "Troubleshooting"
    If the machine doesn't have generated SSH keys or the SSH keys are not assigned to a github account, the checkout will fail with the error `Permission denied (publickey)`. There are two ways to resolve this problem:

    1. Changing the checkout using ssh to https:
    ```
    git config --global url.https://github.com/.insteadOf git@github.com:
    ```
    2. Assign SSH keys to Github account, detailed instruction can be found on [Github help](https:/    /help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)


```shell
git checkout -b arm-modified
wget https://gist.githubusercontent.com/FR4NK-W/cc6661f420fe5e9805d5b9cdb9c41b1b/raw/7dc3b60b86b4c148c2706e3da82eee8d557bbd45/patches_checksum_bench.patch
wget https://gist.githubusercontent.com/FR4NK-W/fb7a4b171ab3d5121b6492b9b664fd47/raw/ddeeb8f2337c64f027955e070df6ef34ff26bf9a/patches_dispatcher.patch

patch ./c/lib/scion/checksum_bench.c ./patches_checksum_bench.patch
rm c/lib/scion/checksum_bench.c.orig
patch ./c/dispatcher/dispatcher.c ./patches_dispatcher.patch
rm c/dispatcher/dispatcher.c.orig
```

```shell
git commit -am "Modified to compile on ARM systems"
bash -c 'yes | GO_INSTALL=true ./env/deps'
```

```shell
sudo cp docker/zoo.cfg /etc/zookeeper/conf/zoo.cfg
./scion.sh build
```
