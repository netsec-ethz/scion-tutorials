# Running SCION on an ARM minicomputer using a prebuilt image

## Introduction

This tutorial will guide you through the steps required to run the SCION infrastructure on ARM minicomputers such as a Raspberry Pi or an Odroid XU4. The list below shows all currently supported devices and what OS will be installed:

* Raspberry Pi Ubuntu MATE
* Rasbperry Pi 2 Ubuntu
* Raspberry Pi 3 Ubuntu
* Odroid XU4 Ubuntu minimal installation
* Odroid XU4 Ubuntu MATE

## Running SCION

Running SCION consists of several steps: registering an account on [SCIONLab Coordination Service](https://www.scionlab.org/), downloading the image corresponding to your device, installing it, and finally running the SCION infrastructure.

### Step One &ndash; downloading a SCION image

In order to download an image, you must login to [SCIONLab Coordination Service](https://www.scionlab.org/). In case you don't have an account yet, follow the registration process.

After logging in, create a new AS by clicking on *Generate a new SCIONLab AS*, select a desired attachment point and give it a label (optional). Choose *Install on a dedicated SCION system (for experts)*. Unless your device has a static, public routable IP address (not behind a NAT), choose *Use an OpenVPN connection for this AS*. Otherwise enter the IP address under wich your device can be reached. Then, under *Create SCION image for IoT device* select your device from the dropdown list and click *Build image*. The procedure may take a few minutes. After it finishes a download link appears from which you can download your generated image. A screenshot of the user interface is shown below:

![SCIONLab download page](/images/scionlab_download_image_arm_setup.png)

### Step Two &ndash; installing the image

The easiest way to install the image on your device is by using [Etcher](https://etcher.io/). Etcher is a graphical SD card writing tool available for Windows, Linux, and macOS. In Etcher select your downloaded *scion.img.bz2* file and flash it to your SD card.

### Step Three &ndash; run the SCION infrastructure

Your device is now ready. You can connect to it via SSH like this (where you replace the IP address with the actual address of your device):

```
    ssh scion@192.168.1.1
```
The password for the scion user is *scion*. It's recommended to change it after the first login.

SCION infrastructer and SCION-viz are started automatically on start up.

## Next steps

After running SCION infrastructure it is necessary to verify that it is running properly. This is covered in tutorial [Verifying SCION Installation](/general_scion_configuration/verifying_scion_installation/).

When the infrastructure is properly running, you have established your SCION AS, congratulations! You can now follow the tutorials listed on the [main page](https://netsec-ethz.github.io/scion-tutorials/) under "Using SCION in projects".
