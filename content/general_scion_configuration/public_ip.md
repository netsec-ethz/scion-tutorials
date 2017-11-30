# Connecting to SCIONLab with a static public IP address

## Introduction

This tutorial will cover steps required for connecting already running SCION infrastructure to SCION lab, doing so you will be running one SCION autonomous system. 

For the purpose of this tutorial, we assume that you **have** a static public IP address and that your machine can receive UDP traffic from internet over port 50000. If this is not the case, please follow [Connecting to SCION Lab over VPN](/general_scion_configuration/vpn_setup)

## Prerequisites

In order to follow this tutorial, we will assume that you already installed SCION infrastructure and that you are able to [run local topology](/general_scion_configuration/local_top).

!!! hint
    If you are running one of the SCION Virtual machine setups, configuration covered in this tutorial is already implemented in the system image. 

## Step One - downloading SCION Lab configuration

In order to download necessary configuration you must login to [SCION Coordination Service](https://coord.scionproto.net/#/login). In case you don't yet have an account, follow the registration process.

!!! note ""
    Since current version of *Coordination Service* only generates VM configuration scripts, we will use them in following steps to configure running SCION infrastructure.

After logging in, select "My Host has a static public IP address and can receive traffic at port 50000" checkbox and enter your public IP in the text field. Afterwards click on **Create and Download SCIONLab VM Configuration** as presented in the image below:

![SCIONLab download page](/images/scionlab_download_vm_static_ip_setup.png)

Navigate to download directory and extract archive content:

```shell
cd ~/Downloads
tar -vxzf scion_lab_<user_email>.tar.gz
cd <user_email>
```

Extracted content should have following file structure:

```
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

- directory `gen` - SCION infrastructure configuration

## Step Two - copying SCION Lab configuration

Before copying new configuration to your SCION directory, you should delete the old one. If necessary back it up previously.

```shell
rm -rf $SC/gen
```

Copy new configuration and navigate to SCION root directory:

```shell
cp -r gen $SC/
cd $SC
```

## Step Three - Restarting SCION infrastructure

After new configuration is copied, you need to restart infrastructure in following way:

```shell
./scion.sh stop
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```

## Next steps

After running the SCION infrastructure, it is necessary to verify that it is running properly. This is covered in the tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation)