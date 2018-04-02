# Installing SCION on an Android device

## Introduction
It is possible to run SCION on an Android device. For this purpose the [Termux](https://github.com/termux/termux-app) app is used, which emulates a Terminal environment with the Linux base system that Android is based upon. The Termux app can be downloaded [on the Google Play Store](https://play.google.com/store/apps/details?id=com.termux).

This tutorial is primarily targeted at running a SCION endhost within Termux. While it is also possible to run an entire SCION AS on an Android device, this currently doesn't run stable within Termux, as it requires Apache Zookeeper, which frequently crashes the Termux environment.

## Prerequisites

It is recommended to make yourself familiar with Termux by reading the [Wiki](https://wiki.termux.com/wiki/Main_Page) to learn how the app can be used comfortably.

To install SCION within Termux it is recommended to access the Termux environment via Android Debug Bridge (ADB) or via SSH.

### Access Termux via SSH

First install the `openssh` package within Termux with `pkg install openssh`, then start the server with `sshd`. Password authentication is not supported, so you need to add your public key to `$HOME/.ssh/authorized_keys`. The ssh server runs by default on port 8022, so connect to it with `ssh -p 8022 DEVICE_IP`. You can find the device IP address with `ip addr list wlan0`. 

### Install necessary packages

Install the required packages from within Termux:

```shell
apt update && apt upgrade
pkg install -y termux-exec git python python2 clang make python-dev libffi-dev openssl-dev openssl-tool curl
```

To access the SD card from Termux, it is required to run `termux-setup-storage` from the Termux console.

### Configure Go workspace

SCION requires a specific Go version. The Termux Go package may be ahead of that version. The following repository offers prebuilt Go packages in the required version for ARMv8/aarch64 architectures:

```shell
curl -O https://raw.githubusercontent.com/stschwar/scion/termux-modifications/debian-packages/golang-doc_2%3A1.9.4_aarch64.deb
curl -O https://raw.githubusercontent.com/stschwar/scion/termux-modifications/debian-packages/golang_2%3A1.9.4_aarch64.deb
dpkg -i golang_2%3A1.9.4_aarch64.deb golang-doc_2%3A1.9.4_aarch64.deb
```

!!! note
    If you require ARMv7-compatible packages, you must build them yourself through [termux-packages](https://github.com/termux/termux-packages).

Setup the Go workspace and add it to your path:

```shell
echo 'export GOPATH="$HOME/go"' >> ~/.profile
source ~/.profile
mkdir -p "$GOPATH/bin"
echo 'PATH=$PATH:$GOPATH/bin' >> ~/.profile
source ~/.profile
```

## Install SCION

### Step One &ndash; clone the SCION repository

After the Go workspace has been configured, we can checkout SCION with the required Termux modifications from Github and apply a required patch using the following commands:

```shell
mkdir -p "$GOPATH/src/github.com/scionproto/scion"
cd "$GOPATH/src/github.com/scionproto/scion"
git config --global url.https://github.com/.insteadOf git@github.com:
git clone --recursive -b termux-modifications git@github.com:stschwar/scion .

curl -O https://raw.githubusercontent.com/stschwar/scion/termux-modifications/patches/lwip-contrib.patch
patch sub/lwip-contrib/ports/unix/proj/scion/Makefile lwip-contrib.patch
rm lwip-contrib.patch
```

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

### Step Three &ndash; install required packages/patches

SCION has an install script to install all necessary dependencies. In the Termux environment, however, this is not yet working. So the dependencies have to be installed manually.

#### Cap'n Proto

To install [Cap'n Proto](https://capnproto.org/) in Termux run the following commands in the `home/` directory:

```shell
curl -O https://capnproto.org/capnproto-c++-0.6.1.tar.gz
tar zxf capnproto-c++-0.6.1.tar.gz
cd capnproto-c++-0.6.1
```
On Termux Cap'n Proto requires some patching to compile:
```shell
cd src/kj/
curl -O https://raw.githubusercontent.com/stschwar/scion/termux-modifications/patches/capnproto-c++-0.6.1/debug.c++.patch
patch debug.c++ debug.c++.patch
rm debug.c++.patch
```

Back in the `capnproto-c++-0.6.1/` root directory run:
```shell
./configure --prefix=$PREFIX TMPDIR=$PREFIX/tmp
make 
make install
```

#### zlog

Install [zlog](https://github.com/HardySimpson/zlog) by following its install instructions mostly. It requires some more patching and the installation of `libandroid-glob-dev`:

```shell
curl https://codeload.github.com/HardySimpson/zlog/tar.gz/latest-stable --output zlog-latest-stable.tar.gz
tar -zxf zlog-latest-stable.tar.gz
cd zlog-latest-stable/
curl -O https://raw.githubusercontent.com/stschwar/scion/termux-modifications/patches/zlog-makefile.patch
patch zlog-makefile.patch
rm zlog-makefile.patch

pkg install -y libandroid-glob-dev
make PREFIX=$PREFIX
make PREFIX=$PREFIX install
```

#### uthash

Install the [uthash](https://troydhanson.github.io/uthash/) library from your `home/` directory:
```shell
curl -o uthash-master.zip https://codeload.github.com/troydhanson/uthash/zip/master
unzip uthash-master.zip
cp uthash-master/src/*.h $PREFIX/include
rm -rf uthash-master/
```

#### SCION Python dependencies

Most of the Python dependencies can easily be installed through `pip`:
```shell
cd $SC
pip2 install -r env/pip2/requirements.txt
pip3 install -r env/pip3/requirements.txt
TMPDIR=$PREFIX/tmp pip3 install lz4 PyNaCl PyYAML Pygments
```
!!! warning "Supervisor"
    In case the pip installation of the package "Supervisor" fails, you can install it manually:

```shell
curl -O https://pypi.python.org/packages/44/60/698e54b4a4a9b956b2d709b4b7b676119c833d811d53ee2500f1b5e96dc3/supervisor-3.3.4.tar.gz
tar -xzf supervisor-3.3.4.tar.gz
cd supervisor-3.3.4/
python2 setup.py install
```

#### SCION Go dependencies

With Go correctly installed it is easy to install the SCION dependencies as well:

```shell
cd $SC/env/go
./deps
```

## Next steps

After finishing the installation of SCION, there are different ways of running different topologies. The following tutorials will cover this in further detail:

1. [Running a local network topology](/general_scion_configuration/local_top/) &ndash; Generate a sample topology and run SCION locally
1. [Connecting to SCIONLab as an endhost](/general_scion_configuration/setup_endhost/) &ndash; Connect to the already running SCION topology as a mobile endhost through an existing SCION setup.

#### Changes to gen folder

Note that in `gen/ISDx/AS10xx/supervisord.conf` the path of the SCION Deamon socket needs to be changed as follows: `"--api-addr" "/data/data/com.termux/files/run/shm/sciond/sdX-10XX.sock"`. 

#### VPN Connection to SCIONLab

Unfortunately, OpenVPN is not currently supported from within the Termux environment. Alternatively, the Open
VPN app can be installed to connect to SCIONLab via VPN. The `client.conf` file that is provided by the SCIONLab coordinator needs to be renamed to `client.ovpn` before it can be imported into the app. Additionally, the line `route 10.0.8.0/24` needs to be added to the file.
