# Installing SCION on an Android device

## Introduction
It is possible to run SCION on an Android device. For this purpose the [Termux](https://github.com/termux/termux-app) app is used, which emulates a Terminal environment with the Linux base system that Android is based upon.

The Termux app can be downloaded [on the Google Play Store](https://play.google.com/store/apps/details?id=com.termux).

## Prerequisites

For the course of this tutorial advanced use of Termux is required. Make yourself familiar with it by reading on the [Wiki](https://wiki.termux.com/wiki/Main_Page) about how the app can be used more comfortably.

The tutorial also expects basic knowledge about how to interact with an Android device through the Android Device Bridge (ADB).

### Install necessary packages

Install the required packages from within Termux:

```shell
pkg install -y termux-setup-storage termux-exec ssh git python python2 clang make python-dev libffi-dev openssl-dev openssl-tools
```

### Configure Go workspace

SCION requires a specific Go version. Termux' Go package is usually ahead of the required version. You can build the required packages yourself (e.g. using [termux-packages](https://github.com/termux/termux-packages)) or use the prebuilt package from the SCION repository:

```shell
curl -O https://raw.githubusercontent.com/stschwar/scion/termux-modifications/debian-packages/golang-doc_2%3A1.9.4_aarch64.deb
curl -O https://raw.githubusercontent.com/stschwar/scion/termux-modifications/debian-packages/golang_2%3A1.9.4_aarch64.deb
dpkg -i golang_2:1.9.4_aarch64.deb golang-doc_2:1.9.4_aarch64.deb
```
!!! note
    There are currently only pre-compiled packages for ARMv8/aarch64 architectures. If you require ARMv7-compatible packages, you must build them yourself through [termux-packages](https://github.com/termux/termux-packages).

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

After the Go workspace has been configured, we can checkout the SCION repository from github.com with all dependencies using the following commands:

```shell
mkdir -p "$GOPATH/src/github.com/scionproto/scion"
cd "$GOPATH/src/github.com/scionproto/scion"
git clone --recursive -b termux-modifications git@github.com:stschwar/scion .
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
./configure TMPDIR=$PREFIX/usr/tmp
make PREFIX=$PREFIX
```
To sucessfully install the libraries, modify the `Makefile` script with an editor (e.g. `vim` or `nano`).
Change line `877` like so:
```diff
-prefix = /usr/local
+prefix = /data/data/com.termux/files/usr/local
```
Finally, run:
```shell
make PREFIX=$PREFIX install
```

#### zlog

Install [zlog](https://github.com/HardySimpson/zlog) by following its install instructions mostly. It requires some more patching:

```shell
tar -zxf zlog-latest-stable.tar.gz
cd zlog-latest-stable/
curl -O https://raw.githubusercontent.com/stschwar/scion/termux-modifications/patches/zlog-makefile.patch
patch zlog-makefile.patch
rm zlog-makefile.patch

make PREFIX=$PREFIX
make PREFIX=$PREFIX install
```

#### uthash

Install the [uthash](https://troydhanson.github.io/uthash/) library in your `home/` directory:
```shell
curl -o uthash-master.zip https://codeload.github.com/troydhanson/uthash/zip/master
unzip uthash-master.zip
cp uthash-master/src/*.h $PREFIX/usr/include
rm -rf uthash-master/
```

#### SCION Python dependencies

Most of the Python dependencies can easilly be installed through `pip`:
```shell
pip2 install -r env/pip2/requirements.txt
pip3 install -r env/pip3/requirements.txt
```
**python-lz4**
```shell
git clone https://github.com/steeve/python-lz4.git
cd python-lz4/
python setup.py install
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