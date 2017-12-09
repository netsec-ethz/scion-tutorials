# Running SCION in a virtual machine &ndash; VPN approach

## Introduction

This tutorial will guide you through the steps required to run the SCION infrastructure in a virtual machine. This tutorial assumes that you don't have a static public IP address or cannot receive traffic on UDP port 50000 from the outside network, and therefore it will use OpenVPN to proxy traffic.

!!! tip
    If you have a static public IP address and you can receive traffic on UDP port 50000, you should consider [running the VM without VPN](static_ip/).

## Prerequisites

Running SCION in a virtual machine requires VirtualBox and Vagrant to be installed on your system.

### Step One &ndash; install VirtualBox

To install VirtualBox, follow the steps on the [VirtualBox download page](https://www.virtualbox.org/wiki/Downloads) for your system. On Ubuntu or similar Linux distributions, you could also install VirtualBox using your package manager:

```shell
apt-get install virtualbox
```

### Step Two &ndash; install Vagrant

To install Vagrant, follow the steps on the [Vagrant download page](https://www.vagrantup.com/downloads.html) for your system. Also, on Ubuntu you could install it via your package manager:

```shell
apt-get install vagrant
```

## Running SCION

Running SCION consists of several steps, registering a SCION VM on [SCIONLab Coordination Service](https://coord.scionproto.net), deploying the VM, and running the SCION infrastructure.

### Step One &ndash; download a SCION VM

In order to download a VM, you must login to [SCIONLab Coordination Service](https://coord.scionproto.net). In case you don't yet have an account, follow the registration process.

After logging in, download a VM configuration by clicking on *Create and Download SCIONLab VM Configuration* as presented in the image below:

![SCIONLab download page](/images/scionlab_download_vm_openvpn_setup.png)

### Step Two &ndash; create and run the VM

When the configuration finishes downloading, extract the archive content in a separate directory. On a Linux system, simply running `tar` command will extract the contents in a separate subdirectory named as your email:

```shell
tar -xvf scion_lab_*.tar.gz
```

After extracting the newly downloaded content, navigate to the extracted directory. It should have the following structure:

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

Verifying that the structure is the same, you can begin the setup by running:

```shell
vagrant box add scion/ubuntu-16.04-64-scion
vagrant box update
vagrant up
```

You will be asked for your password. The installation process will take around 10 minutes.

### Step Three &ndash; run the SCION infrastructure

After successful installation of VM, you will be automatically ssh'ed into machine.

To run SCION you can navigate to the source directory and start the infrastructure with following commands:

```shell
vagrant ssh
```

The SCION infrastructure is automatically started at boot time of your VM. You can control it using the `scion.sh` script located at `~/go/src/github.com/netsec-ethz/scion/`. You can easily get to that directory with `cd $SC`.

## Next steps

After running SCION infrastructure it is necessary to verify that its running properly. This is covered in tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation/)
