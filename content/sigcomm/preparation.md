---
title: Preparation for the Tutorial
parent: SIGCOMM2020 SCION Tutorial
nav_order: 10
---

# Preparation for the Tutorial

To avoid delays due to software downloads and installation during the tutorial and to be able to gauge the number of participants, please complete the following steps before the start of the tutorial (i.e., before 1:30pm EDT on August 14):

1. Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads) and [Vagrant](https://www.vagrantup.com/downloads.html) on the machine you intend to use in the tutorial.
2. Sign up at [SCIONLab](https://www.scionlab.org). In the registration form, please include "SIGCOMM2020" in the `Organisation` field.
3. Create a SCIONLab AS:
   * Navigate to `My ASes` and click `Create a new SCIONLab AS`.
   * Select the geographically closest attachment points.
   * Enable `Use VPN` and select the installation type `Run SCION in a Vagrant virtual machine`.
   * Confirm by clicking `Create AS`.
4. Download the generated tarfile, extract the `Vagrantfile` and start the VM by executing `vagrant up`.
5. Using the SCIONLab Webapp, check the health status of your AS:
   * Enter the virtual machine with `vagrant ssh`.
   * Install SCIONLab's webapp with `sudo apt-get install scion-apps-webapp`.
   * Start the daemon with `sudo systemctl start scion-webapp.service`. The SCIONLab Webapp should be running now under port 8000 of the virtual machine. 
   * In your host machine, outside of the virtual machine, open a browser and navigate to [http://localhost:8000/](http://localhost:8000/). Click on the health tab of the top bar. Every test should be green. If not, you can report the problem to us. Be sure to mention you will be part of the SIGCOMM Workshop when you [contact us](https://docs.scionlab.org/#contact).

For further information please refer to the general tutorial [VM installation page](../install/vm.html).


## Troubleshooting

VirtualBox has some issues on macOS Catalina. If you encounter any of these issues, these remediations may help to get it running:
- Provide permissions to VirtualBox:
  - If installation does not seem to progress, open the "System Preferences", select "Security & Privacy" and enable the installer to run (in the "General" tab you can click to permit the installer to execute)
  - In the "Privacy" tab, provide access to VirtualBox for the following categories: "Full Disk Access", "Accessibility".
- If running the SCION setup with Vagrant hangs, then reboot the machine (you can also manually kill all the VirtualBox processes), and then start the VirtualBox GUI, click on the SCIONLabVM machine, select "Settings", then select the "System" tab, then select the "Processor" tab. Here, select only a single CPU, instead of the configured 2 CPUs.
- If all this fails to get VirtualBox to run, we recommend installing the latest version of the old VirtualBox 5.2, which you can obtain here:
  [https://www.virtualbox.org/wiki/Download_Old_Builds_5_2](https://www.virtualbox.org/wiki/Download_Old_Builds_5_2)
