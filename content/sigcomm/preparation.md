---
title: Preparation for the Tutorial
parent: SIGCOMM2020 SCION Tutorial
nav_order: 10
---

# Preparation for the Tutorial

To avoid delays due to software downloads and installation during the tutorial and to be able to gauge the number of participants, please complete the following steps before the start of the tutorial (i.e., before 1:30pm EDT on August 14):

1. Install [VirtualBox](https://www.virtualbox.org) and [Vagrant](https://www.vagrantup.com) on the machine you intend to use in the tutorial.
2. Sign up at [SCIONLab](https://www.scionlab.org). In the registration form, please include "SIGCOMM2020" in the `Organisation` field.
3. Create a SCIONLab AS:
   * Navigate to `My ASes` and click `Create a new SCIONLab AS`.
   * Select the geographically closest attachment points.
   * Enable `Use VPN` and select the installation type `Run SCION in a Vagrant virtual machine`.
   * Confirm by clicking `Create AS`.
4. Download the generated tarfile, extract the `Vagrantfile` and start the VM by executing `vagrant up`.

Relevant troubleshooting information is provided below.
