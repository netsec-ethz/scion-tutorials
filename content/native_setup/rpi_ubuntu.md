# Building SCION for a Raspberry Pi &ndash; Ubuntu MATE

## Introduction

The SCION infrastructure can also be run on IoT devices like a Raspberry Pi. Building SCION for a Raspberry Pi is similar to the [regular x86 build](ubuntu_x86_build/), although there are a few additional steps required to make everything work.

## Prerequisites

In this tutorial, we assume that you already have a Raspberry Pi running Ubuntu MATE (or similar Ubuntu Xenial based distribution). In order to install Ubuntu MATE, please follow the [installation guide](https://ubuntu-mate.org/raspberry-pi/).

!!! note "Update packages to latest version"
    It is recommended to update all packages before starting the installation process of SCION:

    ```
    sudo apt update && sudo apt upgrade
    ```

## Install necessary tools

!!! note "Note about `sudo`"
    Many of our build and installation scripts will use `sudo` in them. The user has to belong to the `sudo` group:

    `sudo usermod -aG sudo scionuser`

    And it is highly recommended to enable `sudo` without password:

    `echo 'scionuser ALL=(ALL) NOPASSWD:ALL' | sudo tee /etc/sudoers.d/99-scionuser-user`

    Remember to replace `scionuser` with your username.


### Install necessary packages

```shell
sudo apt install git curl
```

### Configure Go workspace

!!! tip
    You can skip this step if you have Go workspaces already configured.

It is necessary to set up your [Go workspace](https://golang.org/doc/code.html#GOPATH "Go workspace"). The following commands will create a default workspace at `~/go` and export it as the `$GOPATH` environment variable:

```shell
echo 'export GOPATH="$HOME/go"' >> ~/.profile
source ~/.profile
mkdir -p "$GOPATH/bin"
echo 'PATH=$PATH:/usr/local/go/bin:$GOPATH/bin' >> ~/.profile
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

On ARM architectures, it is necessary to apply two patches as follows:

```shell
curl https://gist.githubusercontent.com/juagargi/f007a3a80058895d81a72651af32cb44/raw/421d8bfecdd225a3b17a18ec1c1e1bf86c436b35/arm-scionlab-update2.patch | patch -p1
```

In order to enable updating the system, we commit the patched changes into the local arm-modified branch:

```shell
git commit -am "Modified to compile on ARM systems"
```

!!! warning "Troubleshooting"
    If your git identity is not configured, commits won't be possible. Configuring the user identity on a newly installed git can be done as follows:

```shell
cd $SC
git config user.name "John Doe"
git config user.email johndoe@example.com
```

### Step Three &ndash; configure Python path variable

Some SCION components like SCIONviz require Python libraries which are located in the scion root directory. In order to make them accessible, exporting the `PYTHONPATH` environment variable is required:

```shell
echo 'export PYTHONPATH="$SC/python:$SC"' >> ~/.profile
source ~/.profile
```

### Step Four &ndash; finish installing the required packages

The build process is quite demanding in memory, so the machine has to have more than the 1GB of main memory that it comes with. To add one more gigabyte from the swap follow these steps:

```shell
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
free -h
```

In order to instal dependencies, simply issue the following command while in the root directory of the SCION installation:

```shell
bash -c 'yes | GO_INSTALL=true ./env/deps'
./scion.sh build
```

!!! note
    You might be asked to enter the sudo password after running the command

This will finish installing the required dependencies and system packages.

### Step Five &ndash; configure the host Zookeeper instance

Replacing `/etc/zookeeper/conf/zoo.cfg` with the file `docker/zoo.cfg` is recommended. This has the standard parameters set, as well as using a ram disk for the data log, which greatly improves the performance of Zookeeper (at the cost of reliability, so it should only be done in a testing environment).

```shell
sudo cp docker/zoo.cfg /etc/zookeeper/conf/zoo.cfg
```

## Next steps

After finishing the installation of SCION, there are different ways of running different topologies. The following tutorials will cover this in further detail:

1. [Configure SCION to run on system boot](/scion_tricks/setup_startup.md) &ndash; Use systemd to run SCION and SCION-viz when the system is started.
1. [Running a local network topology](/general_scion_configuration/local_top/) &ndash; Generate a sample topology and run SCION locally
1. [Connecting to SCIONLab with a static public IP address](/general_scion_configuration/public_ip/) &ndash; Connect to the already running SCION topology.
1. [Connecting to SCIONLab with a static public IP address, but behind a NAT](/general_scion_configuration/public_ip_nat/)
1. [Connecting to SCIONLab without a static public IP address](/general_scion_configuration/vpn_setup/) &ndash; Connect to the already running SCION topology through an OpenVPN tunnel.
