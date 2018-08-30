# Running SCION in a virtual machine &ndash; VPN approach

## Introduction

This tutorial will guide you through the steps required to run the SCION infrastructure in a virtual machine. This tutorial assumes that you don't have a static public IP address or cannot receive traffic on UDP port 50000 from the outside network, and therefore it will use OpenVPN to connect to the neighboring AS border router.

!!! tip
    If you have a static public IP address and you can receive traffic on UDP port 50000, you could consider [running the VM without VPN](static_ip/).

In summary, the steps you will read about three installation steps, and how to log into your Virtual Machine:

* 1) Configure a Virtual Machine with SCION
* 2) Install VirtualBox and Vagrant
* 3) Run that Virtual Machine
* 4) Log into your Virtual Machine

!!! tip
    Skip some steps: if your host machine is running MacOS or a Debian based Linux, you could do the first step, and then directly execute the `run.sh` script available within the configuration file you have downloaded from the [SCIONLab Coordination Service](https://www.scionlab.org/). This `run.sh` script will download VirtualBox and Vagrant for you and install them (in Debian-Linux it uses `apt-get` and in MacOS, `Homebrew`; you might be asked for your host machine user password when installing), and then it will run the Virtual Machine for you, effectively completing steps 2 and 3. 
    
    But if something does not work with the script, you don't have the required OS, or you want to do it manually, you can also do all the steps without `run.sh`.

## Step One &ndash; downloading a SCION VM

In order to download a VM, you must login to [SCIONLab Coordination Service](https://www.scionlab.org/). In case you don't have an account yet, follow the registration process.

After logging in, create a new AS by clicking on *Generate a new SCIONLab AS*, select a desired attachment point, and choose *Install inside a virtual machine*. Choose *Use an OpenVPN connection for this AS*. Then download a VM configuration by clicking on *Create and Download SCIONLab VM Configuration*. A screenshot of the user interface is shown below:

![SCIONLab download page](/images/scionlab_download_vm_openvpn_setup.png)

You have downloaded the configuration directory in the form of a compressed tar file (`tgz`). 

You will need to uncompress this folder. For this, create a directory that will host your SCIONLab content. For instance:

```shell
mkdir ~/scionlab
```

And move the contents to your SCIONLab directory, in the example:
```shell
cd ~/scionlab
mv ~/Download/scion_lab_my_filename.tar.gz .
```

Next, extract the archive content. On a Linux system, simply running `tar` command will extract the contents:
```shell
tar -xvzf scion_lab_my_filename.tar.gz
```

!!! tip
    If you experience any issue while extracting or uncompressing the file, check if your browser has uncompress it for you by typing `file scion_lab_*.t*`

If your downloader automatically uncompressed the downloaded file, the `.gz` extension is many times missing, or the type of the file not specifying compressed. You can extract the contents with:
```shell
tar -xvf scion_lab_my_filename.tar
```

After extraction, the extracted directory has the following structure:

```
my_filename
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

It is now when you can execute `run.sh`, as we have mentioned in the introduction, and skip the next two steps. Otherwise, keep on reading.


## Step Two &ndash; Install VirtualBox and Vagrant

You need to this only once in your host machine; if you have already done it, skip this step. Running SCION in a virtual machine requires VirtualBox and Vagrant to be installed on your system. 

### Install VirtualBox

To install VirtualBox, follow the steps on the [VirtualBox download page](https://www.virtualbox.org/wiki/Downloads) for your system. On Ubuntu or similar Linux distributions, you could also install VirtualBox using your package manager:

```shell
sudo apt-get install virtualbox
```

### Install Vagrant

To install Vagrant, follow the steps on the [Vagrant download page](https://www.vagrantup.com/downloads.html) for your system. Also, on Ubuntu you could install it via your package manager:

```shell
sudo apt-get install vagrant
```


## Step Three &ndash; run the Virtual Machine

Verifying the structure, you can begin the setup by running:

```shell
cd my_filename
vagrant box add scion/ubuntu-16.04-64-scion
vagrant box update
vagrant up
```

The installation process will take around 10 minutes. 

### Wait, what has just happened?

If you are interested, this is exactly what those steps are doing:

* `vagrant box add scion/ubuntu-16.04-64-scion` will add the base SCION box to your filesystem. You only need to do this once, as the same base box is used for all SCION Virtual Machines.
* `vagrant box update` will ensure that the base box is the latest one. You would run this every time you want to install a new VM, but it is not necessary to do it if you only want to use one of your already installed Virtual Machines.
* `vagrant up` will run the Virtual Machine. You need to do this every time you install a new Virtual Machine, or if you stop your host machine. This, essentially, is booting up the Virtual Machine, and installing it if it was the first time.

If you want to stop your Virtual Machine, you will run `vagrant halt`. Then, to boot it up again, `vagrant up`. These two commands stop and start the Virtual Machine.



!!! Warning
    If for whatever reason you want to completely destroy and remove your existing Virtual Machine, you would run `vagrant destroy`. Be careful, as you would remove everything that was inside that Virtual Machine.

## Step Four &ndash; Use the SCION Virtual Machine 

After successful installation of the VM, you can ssh into your VM:

```shell
vagrant ssh
```

The SCION infrastructure is automatically started at boot time of your VM. You can control it using the `scion.sh` script located at `~/go/src/github.com/scionproto/scion/`. You can easily get to that directory with `cd $SC`.

To shut the system down, you can type `sudo shutdown now` inside the VM, or `vagrant halt` in the host terminal.

After the installation, to start the VM, you can use `vagrant up`, followed by `vagrant ssh` to start a VM shell.

## Almost Done: Validate and More

After running SCION infrastructure it is necessary to verify that it is running properly. This is covered in tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation/).

When the infrastructure is properly running, you have established your SCION AS, congratulations! You can now follow the tutorials listed on the [main page](https://netsec-ethz.github.io/scion-tutorials/) under "Using SCION in projects".
