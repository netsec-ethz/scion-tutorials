# Connecting to SCIONLab via VPN

!!! warning "Note"
    This page needs to be updated. Use with care.

## Introduction

This tutorial will cover the steps required for connecting a SCION installation to SCIONLab. In the end, you will be running one SCION autonomous system connected to the SCIONLab network.

For the purpose of this tutorial, we assume that you do not have static public IP address, or that your machine cannot receive UDP traffic on port 50000 from the Internet. If this is not the case, you should consider the tutorial [Connecting to SCION Lab with public IP](/general_scion_configuration/public_ip/) or [Connecting to SCION Lab with public IP behind a NAT](/general_scion_configuration/public_ip_nat/).

## Prerequisites

In order to follow this tutorial, we will assume that you already installed the SCION infrastructure and that you are able to [run a local topology](/general_scion_configuration/local_top/).

!!! hint
    If you are running one of the SCION Virtual Machine setups, the configuration covered in this tutorial is already implemented in the system image, so you don't need the steps described here.

## Step One - installing OpenVPN

In order to circumvent the problem of not having a publicly accessible IP address, we create an OpenVPN tunnel to carry the SCION traffic between the two SCION border routers.

In order to install the openvpn client, you can simply run:

```shell
sudo apt install openvpn
```

## Step Two - downloading SCION Lab configuration

In order to download the necessary configuration you must login to [SCION Coordination Service](https://www.scionlab.org/). In case you don't yet have an account, follow the registration process.

!!! note ""
    Since the current version of the *Coordination Service* only generates VM configuration scripts, we will use them in the following steps to configure the running SCION infrastructure.

After logging in, download a VM configuration by clicking on **Create and Download SCIONLab VM Configuration** as presented in the image below:

![SCIONLab download page](/images/scionlab_download_vm_openvpn_setup.png)

Navigate to the download directory and extract the archive content:

```shell
cd ~/Downloads
tar -vxzf scion_lab_<user_email>.tar.gz
cd <user_email>
```

The extracted content should have the following file structure:

```
├── client.conf
├── gen
│   ├── dispatcher
│   │   ├── dispatcher.zlog.conf
│   │   └── supervisord.conf
│   └── ISD1
│       └── ...
├── README.md
├── run.sh
├── scion.service
├── ...
├── scion-viz.service
└── Vagrantfile

```

For the purpose of this tutorial we will just need:

- file `client.conf` - client OpenVPN configuration
- directory `gen` - SCION infrastructure configuration

## Step Three - Connecting to OpenVPN server

Adding the OpenVPN configuration is accomplished by copying files to the openvpn directory:

```shell
sudo cp client.conf /etc/openvpn
sudo chmod 600 /etc/openvpn/client.conf
```

Next, we need to automatically launch the OpenVPN service on startup of the system:

```shell
systemctl start openvpn@client
systemctl enable openvpn@client
```

After this, you should verify that new `tun` interface is added. The command:

```shell
ip a
```

should display the newly added interface, in this case `tun0` as in this example:

```
9: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    link/none 
    inet 10.0.8.40/24 brd 10.0.8.255 scope global tun0
       valid_lft forever preferred_lft forever
    inet6 fe80::2337:a0c4:7fa7:78b3/64 scope link flags 800 
       valid_lft forever preferred_lft forever
```

In this case, the client's OpenVPN IP address is: `10.0.8.40`.

## Step Four - copying SCION Lab configuration

Before copying the new configuration to your SCION directory, you should delete the old one. If necessary back it up previously.

```shell
rm -rf $SC/gen
```

Copy new configuration and navigate to SCION root directory:

```shell
cp -r gen $SC/
cd $SC
```

## Step Five - Restarting SCION Infrastructure

After the OpenVPN connection is established and the new configuration is copied, you need to restart the infrastructure as follows:

```shell
./scion.sh stop
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```

## Next steps

After running the SCION infrastructure, it is necessary to verify that it is running properly. This is covered in the tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation/).
