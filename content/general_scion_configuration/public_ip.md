# Connecting to SCIONLab with a static public IP address

!!! warning "Note"
    This page needs to be updated. Use with care.

## Introduction

This tutorial will cover the steps required for connecting a SCION installation to SCIONLab. In the end, you will be running one SCION autonomous system connected to the SCIONLab network.

For the purpose of this tutorial, we assume that you **have** a static public IP address and that your machine can receive UDP traffic from the Internet on port 50000. If this is not the case, please follow [Connecting to SCION Lab over OpenVPN](/general_scion_configuration/vpn_setup/)

!!! hint
    Sometimes, providers change the IP address of customers unexpectedly. If the IP address changes, then unfortunately the SCION connection to the border router also fails, and then the connection needs to be torn down and re-established from the SCIONLab.org web site. Another approach is to use the approach using a OpenVPN connection, described in the [OpenVPN connection tutorial](/general_scion_configuration/vpn_setup/).

## Prerequisites

In order to follow this tutorial, we will assume that you already installed the SCION infrastructure and that you are able to [run a local topology](/general_scion_configuration/local_top/).

!!! hint
    If you are running one of the SCION Virtual Machine setups, the configuration covered in this tutorial is already implemented in the system image, so you don't need the steps described here.

## Step One - downloading SCION Lab configuration

In order to download necessary configuration you must login to [SCION Coordination Service](https://www.scionlab.org/). In case you don't yet have an account, follow the registration process.

!!! note ""
    Since the current version of the *Coordination Service* only generates VM configuration scripts, we will use them in the following steps to configure running a SCION infrastructure on a native system.

After logging in, select "My Host has a static public IP address and can receive traffic at port 50000" checkbox and enter your public IP in the text field. Afterwards click on **Create and Download SCIONLab VM Configuration** as presented in the image below:

![SCIONLab download page](/images/scionlab_download_vm_static_ip_setup.png)

Navigate to the download directory and extract the archived content:

```shell
cd ~/Downloads
tar -xvzf scion_lab_<user_email>.tar.gz
cd <user_email>
```

The extracted content should have the following file structure:

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
├── ...
├── scion-viz.service
└── Vagrantfile

```

For the purpose of this tutorial we will just need the directory `gen`, which contains the SCION infrastructure configuration.

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

After the new configuration is copied, you need to restart the infrastructure in the following way:

```shell
./scion.sh stop
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```

## Next steps

After running the SCION infrastructure, it is necessary to verify that it is running properly. This is covered in the tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation/).
