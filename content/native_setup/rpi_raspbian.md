# Building SCION for Raspberry PI - Raspbian

## Introduction

SCION infrastructure can be run on IoT devices like Raspberry PI. Building SCION for Raspberry PI is similar to regular x86 build, although there are few additional steps required to make everything work.

## Prerequisites

In this tutorial we assume that you already have Raspberry PI running Raspbian. In order to install Raspbian, please follow [installation guide](https://www.raspberrypi.org/downloads/raspbian/)

!!! note "Update packages to latest version"
    It is recommended to update packages in your package

    ```
    sudo apt update && sudo apt upgrade
    ```

## Installing necessary tools

### Install necessary packages

```shell
sudo apt install git openvpn
```

### Install GO

In order to run SCION, go version `1.8.x` is required. You can download and install go with following commands:

```shell
wget https://storage.googleapis.com/golang/go1.8.5.linux-armv6l.tar.gz
sudo tar -C /usr/local -xzf go1.8.5.linux-armv6l.tar.gz

echo 'PATH=$PATH:/usr/local/go/bin' >> ~/.profile
source ~/.profile
```

### Configure GO workspace

After installation of Go tools, it is necessary to create go workspace with following commands:

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
    If the machine doesn't have generated ssh keys or ssh keys are not assigned to github account, checkout will fail with following error: `Permission denied (publickey)`. There are two ways to resolve this problem:

    1. Changing checkout using ssh to https: 
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