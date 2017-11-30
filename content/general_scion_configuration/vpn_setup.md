# Connecting to SCIONLab via VPN

## Introduction

This tutorial will cover steps required for connecting already running SCION infrastructure to SCION lab, doing so you will be running one SCION autonomous system. 

For the purpose of this tutorial, we assume that you don't have static public IP address, or that your machine cannot receive UDP traffic on port 50000 from internet. If this is not the case, you should consider following [Connecting to SCION Lab with public IP](/general_scion_configuration/public_ip)

## Prerequisites

In order to follow this tutorial, we will assume that you already installed SCION infrastructure and that you are able to [run local topology](/general_scion_configuration/local_top).

!!! hint
    If you are running one of the SCION Virtual machine setups, configuration covered in this tutorial is already implemented in the system image. 

## Step One - installing OpenVPN

In order to circumvent problem of not having publicly accessible IP address, we can tunnel SCION traffic to an exit node that has it, using OpenVPN.

In order to install openvpn client, you can simply run:

```shell
sudo apt install openvpn
```

## Step Two - downloading SCION Lab configuration

In order to download necessary configuration you must login to [SCION Coordination Service](https://coord.scionproto.net/#/login). In case you don't yet have an account, follow the registration process.

!!! note ""
    Since current version of *Coordination Service* only generates VM configuration scripts, we will use them in following steps to configure running SCION infrastructure.

After logging in, download a VM configuration by clicking on **Create and Download SCIONLab VM Configuration** as presented in the image below:

![SCIONLab download page](/images/scionlab_download_vm_openvpn_setup.png)

Navigate to download directory and extract archive content:

```shell
cd ~/Downloads
tar -vxzf scion_lab_<user_email>.tar.gz
cd <user_email>
```

Extracted content should have following file structure:

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
├── scion-viz.service
└── Vagrantfile

```

For the purpose of this tutorial we will just need:

- file `client.conf` - client OpenVPN configuration
- directory `gen` - SCION infrastructure configuration

## Step Three - Connecting to OpenVPN server

Adding VPN configuration is done by copying files in openvpn directory:

```shell
sudo cp client.conf /etc/openvpn
sudo chmod 600 /etc/openvpn/client.conf
```

Starting OpenVPN service, and enabling it to run on startup is done in following way:

```shell
systemctl start openvpn@client
systemctl enable openvpn@client
```

After this, you should verify that new `tun` interface is added. 

Running 

```shell
ip a
```

should display newly added interface, in this case `tun0`:

```
9: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    link/none 
    inet 10.0.8.40/24 brd 10.0.8.255 scope global tun0
       valid_lft forever preferred_lft forever
    inet6 fe80::2337:a0c4:7fa7:78b3/64 scope link flags 800 
       valid_lft forever preferred_lft forever
```

In this case client's OpenVPN IP address is: `10.0.8.40`, this address might vary on different setups, please keep a not of yours.

## Step Four - copying SCION Lab configuration

Before copying new configuration to your SCION directory, you should delete the old one. If necessary back it up previously.

```shell
rm -rf $SC/gen
```

Copy new configuration and navigate to SCION root directory:

```shell
cp -r gen $SC/
cd $SC
```

Because `gen` directory downloaded from Coordination Service is customized for VM IP addresses (`10.0.2.15`), we need to replace them with address obtained from OpenVPN on current system. In following commands we will assume that OpenVPN IP address is: `10.0.8.40` but you should use the value obtained in previous step:

```shell
find ./gen/ -name "*.json" -exec sed -i "s/10.0.2.15/10.0.8.40/g" '{}' \;
find ./gen/ -name "*.yml" -exec sed -i "s/10.0.2.15/10.0.8.40/g" '{}' \;
find ./gen/ -name "*.conf" -exec sed -i "s/10.0.2.15/10.0.8.40/g" '{}' \;
```

## Step Five - Restarting SCION infrastructure

After OpenVPN connection is established and new configuration is copied, you need to restart infrastructure in following way:

```shell
./scion.sh stop
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```

## Next steps

After running the SCION infrastructure, it is necessary to verify that it is running properly. This is covered in the tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation)