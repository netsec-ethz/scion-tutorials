---
title: Adding Wireshark and Tshark SCION protocol dissector
parent: Tools
nav_order: 10
---

# Adding Wireshark and Tshark SCION protocol dissector

## Introduction

In this tutorial we will add SCION protocol dissector in Wireshark and Tshark. This will allow easier an more intuitive debugging
of SCION protocol. You can read more on what protocol dissector does on [Wireshark docs](https://www.wireshark.org/docs/wsdg_html_chunked/ChapterDissection.html#ChDissectWorks)

## Prerequisites

In order to continue this tutorial, we will assume that you already have Wireshark or Tshark installed on your system.

{% include alert type="Tip" content="Running Wireshark is recommended on machines with graphical interface." %}

### Install Wireshark

In order to install Wireshark, follow installation guide on [Wireshark website](https://www.wireshark.org/) for your platform. 
On Ubuntu, simply run:

```shell
sudo apt install wireshark
```

### Install Tshark

In case you want to install Tshark on Ubuntu simply run:

```shell
sudo apt install tshark
```

## Step One - Finding plugin directory

We need to find directory in which Wireshark or Tshark are looking for plugins so we can place SCION plugin there.

### Wireshark

From **Help** menu select **About Wireshark** and in newly opened window select **Folders** tab. 
There are paths to global and local plugin directory.

In this tutorial we will use global lua plugin directory which is usually:

```
/usr/lib/x86_64-linux-gnu/wireshark/plugins/
```

Plugins from global plugin directory are available to all users, while local is only for currently running user.

### Tshark

In order to find the directory where Tshark is loading plugins from we can run following command:

```
tshark -G folders
```

Output will look something like this:

```
...
Personal Plugins:       /home/user/.local/lib/wireshark/plugins/2.6
Global Plugins:         /usr/lib/x86_64-linux-gnu/wireshark/plugins/2.6
Personal Lua Plugins:   /home/user/.local/lib/wireshark/plugins
Global Lua Plugins:     /usr/lib/x86_64-linux-gnu/wireshark/plugins
Extcap path:            /usr/lib/x86_64-linux-gnu/wireshark/extcap
...
```

Examining output we can conclude that plugins are being loaded from 

```
/usr/lib/x86_64-linux-gnu/wireshark/plugins/
```

So we will use that for our plugin directory.

## Step Two - Adding plugin

Wireshark/Tshark plugin is located in SCION project at `tools/wireshark/scion.lua`.

It is necessary to download `scion.lua` file and place it in plugin directory acquired in previous step.

In Ubuntu system this can be done with following command:

```
sudo wget -P /usr/lib/x86_64-linux-gnu/wireshark/plugins/ https://raw.githubusercontent.com/scionproto/scion/master/tools/wireshark/scion.lua
```

