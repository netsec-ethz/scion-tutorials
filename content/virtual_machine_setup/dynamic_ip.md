# Running SCION in a virtual machine &ndash; VPN approach

## Introduction

This tutorial will guide you through the steps required to run the SCION infrastructure in a virtual machine. This tutorial assumes that you don't have a static public IP address or cannot receive traffic on UDP port 50000 from the outside network, and therefore it will use OpenVPN to connect to the neighboring AS border router.

!!! tip
    If you have a static public IP address and you can receive traffic on UDP port 50000, you should consider [running the VM without VPN](static_ip/).

## Prerequisites

Running SCION in a virtual machine requires VirtualBox and Vagrant to be installed on your system.

### Step One &ndash; install VirtualBox

To install VirtualBox, follow the steps on the [VirtualBox download page](https://www.virtualbox.org/wiki/Downloads) for your system. On Ubuntu or similar Linux distributions, you could also install VirtualBox using your package manager:

```shell
sudo apt-get install virtualbox
```

### Step Two &ndash; install Vagrant

To install Vagrant, follow the steps on the [Vagrant download page](https://www.vagrantup.com/downloads.html) for your system. Also, on Ubuntu you could install it via your package manager:

```shell
sudo apt-get install vagrant
```

## Running SCION

Running SCION consists of several steps: registering a SCION VM on [SCIONLab Coordination Service](https://www.scionlab.org/), deploying the VM, and running the SCION infrastructure.

### Step One &ndash; downloading a SCION VM

In order to download a VM, you must login to [SCIONLab Coordination Service](https://www.scionlab.org/). In case you don't have an account yet, follow the registration process.

After logging in, create a new AS by clicking on *Generate a new SCIONLab AS*, select a desired attachment point, and choose *Install inside a virtual machine*. Unless your VM has public IP address, choose *Use an OpenVPN connection for this AS*. Then download a VM configuration by clicking on *Create and Download SCIONLab VM Configuration*. A screenshot of the user interface is shown below:

![SCIONLab download page](/images/scionlab_download_vm_openvpn_setup.png)

### Step Two &ndash; create and run the VM

Create a directory that will host your SCIONLab content. For instance:
```shell
mkdir ~/scionlab
```

When the configuration finishes downloading, move the contents to your SCIONLab directory, for instance (adjust based on your setup and download directory):
```shell
cd ~/scionlab
mv ~/Download/scion_lab_*.tar* .
```

Next, extract the archive content. On a Linux system, simply running `tar` command will extract the contents:
```shell
tar -xvzf scion_lab_*.tar.gz
```

If your downloader automatically uncompressed the downloaded file, the `.gz` extension is missing, and you can extract the contents with:
```shell
tar -xvf scion_lab_*.tar
```

After extraction, the extracted directory has the following structure:

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
├── scion-viz.service
├── scion.service
├── scionupgrade.service
├── scionupgrade.sh
├── scionupgrade.timer
└── Vagrantfile
```

Verifying the structure, you can begin the setup by running, replacing `*` according to the name of your extracted archive:

```shell
cd scion_lab_*
vagrant box add scion/ubuntu-16.04-64-scion
vagrant box update
vagrant up
```

The installation process will take around 10 minutes.

### Step Three &ndash; run the SCION infrastructure

After successful installation of the VM, you can ssh into your VM:

```shell
vagrant ssh
```

The SCION infrastructure is automatically started at boot time of your VM. You can control it using the `scion.sh` script located at `~/go/src/github.com/scionproto/scion/`. You can easily get to that directory with `cd $SC`.

To shut the system down, you can type `sudo shutdown now` inside the VM, or `vagrant halt` in the host terminal.

After the installation, to start the VM, you can use `vagrant up`, followed by `vagrant ssh` to start a VM shell.

## Next steps

After running SCION infrastructure it is necessary to verify that it is running properly. This is covered in tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation/).

When the infrastructure is properly running, you have established your SCION AS, congratulations! You can now follow the tutorials listed on the [main page](https://netsec-ethz.github.io/scion-tutorials/) under "Using SCION in projects".
