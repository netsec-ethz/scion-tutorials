# SCION Box

!!! warning "Note"
    The SCION box feature is currently not operational. Please come back later.

## Introduction

This tutorial introduces you to SCION box, shows you what you can do with a device connected to a SCION attachment point and with a PCEngines device you might request [here](https://www.scionlab.org/) in particular.

## Setting up the PCEngines device

You received the preconfigured PCEngines device. In the best case, the setup is as single as connecting the device to a network with Internet connectivity via the labelled network interface (the one closest to the serial interface).
You can then check on the scion-coordinator on your [user page](https://www.scionlab.org/#/user) that you device status is online.
    
!!! hint
    In case you local network implements MAC filtering, you need to request your network administrator to allow the PCEngines device to access the network. You will find the MAC address of the device on a sticker next to the network interface.

    The SCION boxes use OpenVPN to connect to SCION attachment points in case you selected this options. Hence OpenVPN needs to be able to make outgoing connections to its attachment point on port 1194. Since the exact address of the attachment point varies depending on your region and might change over time, please request assistance on the user group if you need it to whitelist it.

## Make user of the device

The SCION is running a full AS connected to the SCIONLab infrastructure. This means the device is running the following services: a border router, a beacon server, a certificate server, a path server and a SIBRA server. You can read about the function of each service in the [SCION book](https://www.scion-architecture.net/pdf/SCION-book.pdf).
In addition, SCION boxes also run ithe SCIONviz server which allows you to visualize the paths you AS knows about. You can connect to SCIONviz by connecting a regular IP device to one of the remaining interfaces. The open the webpage http://172.16.1.1:8000 and the SCIONviz will load. For instructions on how to use SCIONviz, please see the [SCIONviz tutorial](/as_visualization/browser_asviz.md) (but make sure to use the address mentioned here).

## Setup an endhost and connect it via the SCION box

Setup an endhost and configure it as described in [Configuring SCION endhost topology](/general\_scion\_configuration/setup\_endhost/).
Then connect the machine to one of the secondary interfaces of you SCION box. Your devices will be allocated a local address.

## Next steps

Check out the [SCION code base](https://github.com/netsec-ethz/scion/) and start contributing.
