---
title: Running as an app (Android)
parent: Installation
nav_order: 40
---


# Running as an app (Android)

{% include alert type="warning" title="Warning: Beta version" content="The SCION Android app is in beta and has only been tested on selected Android devices." %}

The SCION Android app allows you to host a SCION AS on your smartphone. This allows you to request and serve contents from and to the SCIONLab network. All SCION services are configured automatically based on your SCIONLab configuration. For detailed usage instructions, see below.

## Prerequisites
- Android device with at least Android 7.0
- SCION app, [available on the Play Store](https://play.google.com/store/apps/details?id=org.scionlab.scion) (source code [available on GitHub](https://github.com/netsys-lab/scion-android))

## Instructions
To connect to the SCIONLab network with your Android phone, perform the following steps:

1.  Register for a free user account on [scionlab.org](https://www.scionlab.org/).
2.  In "[My ASes](https://www.scionlab.org/user/)", create a new SCIONLab AS with the following settings:
    
    *   _Label_: any value (is ignored by the app)
    *   _Installation type_: Choose **SCION installation from packages**
    *   _Attachment point_: Choose any attachment point you like, preferably the nearest to your location
    *   _Use VPN_: You **must** check this box
    *   _Public Port_: You **must not** change this from its default value 50000
    
    Alternatively, you can also use an existing SCIONLab AS if you adjust its settings as described above. In that case, also make sure that the _Active_ box is checked.
3.  Once you have created the AS, you can download its configuration from "My ASes" as a _.tar.gz_ archive. Store this archive somewhere on your smartphone (e.g., on the SD card).
4.  In the app, press the blue SCION button. The app will ask you to install _OpenVPN for Android_, which is required to connect to SCIONLab. _You do not need to start OpenVPN for Android manually, everything is set up by the SCION app._
5.  Once _OpenVPN for Android_ is installed, press the SCION button again. You may have to grant permissions regarding VPN and media storage. Then choose the _.tar.gz_ SCIONLab configuration you have downloaded previously.
6.  The app will now connect to SCIONLab and ping the given SCION address. To disconnect, press the SCION button again.

## Demo Video
There is a [demo video](https://youtu.be/yQVDg6egeNg) of the app and its configuration.

## Troubleshooting
In the app, you can view all log data from SCION. For more detailed information, have a look at the `/Android/data/org.scionlab.scion/files/` directory in the internal (or, on some devices, external) storage of your phone.
