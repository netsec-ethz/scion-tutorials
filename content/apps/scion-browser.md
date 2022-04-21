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

### Verify host file
Ensure that the following content is in the `/etc/scion/hosts` or `/etc/hosts` file:
```
17-ffaa:0:1101,129.132.121.164 www.scionlab.org
19-ffaa:1:c3f,141.44.25.148 www.scionlab.chat www.scion-pathguess.game
```

### Install extension
**Note:** At the moment, we support only chrome, other browsers will follow.
To install the browser extension, download the [latest release](https://github.com/netsys-lab/scion-browser-extensions/archive/refs/tags/v0.0.1.zip) and unzip the archive. Then navigate to `Extensions`->`Manage Extensions`. On the upper right corner, enable `Developer Mode`. Then click the `Load unpacked` button and select the `chrome` folder in the unzipped folder. The SCION Browser Extension appears now in your list of extensions.

To get started, click the SCION Browser Extension icon and enable SCION forwarding. You should now see a list of SCION-enabled domains containing the ones mentioned below.

**Note:** The error indicating that the manifest is deprecated does not impact the functionality. We will fix this in one of the upcoming versions.

## Usage
The SCION Browser Extension checks for SCION-enabled domains if enabled via the popup clicking on the icon. A domain can be accessed via the configured host name.

### SCION enabled domains

Hostnames can be added or removed from the SCION enabled domains list.
- To add a hostname, type it in the text bar and press the `Add Domain` button.
- To delete a hostname, check the box next to it and press the `Delete Selected` button.

The list contains the hostnames stated below and any hostname present in the `/etc/scion/hosts` or `/etc/hosts` file. Adding or deleting a hostname will not modify those files. Reloading the extension will reset the hostname list.

### Geofencing (Whitelisting)

To configure the ISD whitelist, press the `Options` button. SCION traffic will traverse only those ASes that have been enabled using the toggle button.

![Geofence options](/content/images/geofence_options.png)

For instance, in the image above SCION traffic will be forwarded through ISD-19 in EU and the ISD-17 in Switzerland.

## SCION-enabled Domains
- Mirror of scionlab.org: http://www.scionlab.org
- Sample live chat: http://www.scionlab.chat:9080
- SCION path guessing game: http://www.scion-pathguess.game:8080
