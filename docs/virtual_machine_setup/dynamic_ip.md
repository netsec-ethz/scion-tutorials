# Running SCION in virtual machine - VPN approach

## Introduction

This tutorial will guide you through steps required to run SCION infrastructure in virtual machine. This tutorial assumes that you don't have static public IP address or cannot receive traffic on UDP port 50000 from outside network, and therefore it will use OpenVPN to proxy traffic.

!!! tip
    If you have static public IP address and you can receive traffic on UDP port 50000 you should consider [running VM without VPN](static_ip)

## Prerequisites

Running SION in virtual machine requires VirtualBox and Vagrant to be installed on your system.

### Step One - install VirtualBox

To install VirtualBox follow steps on [VirtualBox download page](https://www.virtualbox.org/wiki/Downloads) for your system. On Ubuntu or similar Linux distro you could also install VirtualBox using packet manager:

```shell
apt-get install virtualbox
```

### Step Two - install Vagrant

To install vagrant follow steps on [Vagrant download page](https://www.vagrantup.com/downloads.html) for your system. Also on Ubuntu you could install it from packet manager:

```shell
apt-get install vagrant
```

## Running SCION

Running SCION consists of several steps, registering SCION VM on [SCIONLab Coordination Service](https://coord.scionproto.net), deploying VM and running SCION infrastructure.

### Step One - Download SCION VM

In order to download VM with you must login to [SCIONLab Coordination Service](https://coord.scionproto.net), in case you don't have an account follow Registration process.

After successfully logging in, download VM configuration by clicking on *Create and Download SCIONLab VM Configuration* as presented in image below:

![SCIONLab download page](/images/scionlab_download_vm_openvpn_setup.png)

### Step Two - Create and run VM

When the configuration finishes downloading, extract archive content in separate directory. On a Linux system simply running `tar` command will extract contents in separate subdirectory named as your email:

```shell
tar -xvf scion_lab_*.tar.gz
```

After extracting newly downloaded content, navigate to extracted directory. It should have following structure:

```
├── client.conf
├── gen
│   ├── dispatcher
│   │   ├── dispatcher.zlog.conf
│   │   └── supervisord.conf
│   └── ISD1
│       └── AS1029
│           └ ...
│
├── README
├── run.sh
└── Vagrantfile
```

Verifying that structure is the same and begin setup by running: 

```shell
./run.sh
```

You will be asked for root password, after entering it follow the steps in the installation process.

### Step Three - Run SCION infrastructure

After successful installation of VM, you will be automatically ssh'ed into machine. 

To run SCION you should navigate to source directory start infrastructure with following commands:

```shell
cd go/src/github.com/netsec-ethz/scion/
./scion.sh run
```

## Next steps

After running SCION infrastructure it is necessary to verify that its running properly. This is covered in tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation)