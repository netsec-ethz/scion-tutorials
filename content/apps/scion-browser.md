---
title: SCION Browser Extension
parent: Applications
nav_order: 100
---

# SCION Browser Extension
The SCION Browser extension provides access to HTTP(S) resources via SCION by using skip as configured proxy for all SCION-enabled domains.

The following components are required:
- [SCION Browser Extension](https://github.com/netsys-lab/scion-browser-extensions): Extensions for Chrome (Firefox later) that enable SCION connectivity
- [Skip](https://github.com/netsec-ethz/scion-apps/tree/master/skip): SCION forwarding proxy

## Setup
In alpha stage, the following steps are required to configure the SCION Browser Extension.

### Build and start skip
To use the SCION Browser Extension, [skip](https://github.com/netsec-ethz/scion-apps/tree/master/skip) must run on the SCION host.

### Install extension
**Note:** At the moment, we support only chromium based browsers (e.g. Chrome, Brave), other browsers will follow.
To install the browser extension, download the [latest release](https://github.com/netsys-lab/scion-browser-extensions/archive/refs/tags/v0.0.3.zip) and unzip the archive. Then navigate to `Extensions`->`Manage Extensions`. On the upper right corner, enable `Developer Mode`. Then click the `Load unpacked` button and select the `chrome` folder in the unzipped folder. The SCION Browser Extension appears now in your list of extensions. To get started, enable the SCION Browser Extension icon and enable SCION forwarding. 

**Note:** The error indicating that the manifest is deprecated does not impact the functionality. We will fix this in one of the upcoming versions.

## Usage
The SCION Browser Extension may work in two modes: In the default mode, the extension loads resources via SCION for SCION-enabled domains and for the rest, it loads them via BGP/IP.

![Default](/content/images/default_extension.png)

In the strict mode, only resources from SCION-enabled domains are loaded.

![Strict](/content/images/strict_extension.png)

### Geofencing (Whitelisting)

To configure the ISD whitelist, press the `Options` button. SCION traffic will traverse only those ASes that have been enabled using the toggle button.

![Geofence options](/content/images/geofence_options.png)

For instance, in the image above SCION traffic will be forwarded through ISD-19 in EU and the ISD-17 in Switzerland.

### Path usage information
The extension provides path information to the user about the path used during the connection. It provides visual information about the ISD treaversed, the exchanged data amount and detailed information about the traversed ASes.

![Path usage](/content/images/path_usage_extension.png)

## SCION-enabled Domains
- Mirror of scionlab.org: http://www.scionlab.org
- Mirror of scion-architecture.net: http://www.scion-architecture.net
- Mirror of netsys.ovgu.de: https://www.netsys.ovgu.de

### Additional domains via the host file
One can also manually add SCION Addresses for specific domains to either the `/etc/scion/hosts` or the `/etc/hosts` file, e.g.,:
```
17-ffaa:0:1101,129.132.121.164 www.yourdomain.org
```
