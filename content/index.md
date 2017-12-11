# Welcome to SCION Tutorials

## Introduction

This website provides step-by-step instructions on how to install and run the SCION infrastructure. It also provides a list of interesting projects that are using the SCION infrastructure for communication.

## Getting started

There are generally two ways of installing and running SCION infrastructure. The first way is by downloading and running a preconfigured Virtual Machine (VM), while the second way is manual installation on an Ubuntu 16.04 platform. We cover both approaches in this tutorial.

We suggest exploring the tips and tricks section below to learn how to use the infrastructure.

## Running SCION infrastructure in a VM

The easiest way to run SCION is by running a preconfigured SCION Virtual Machine on a commodity OS (MacOS, Windows). The following tutorials are covering the necessary steps.

* [Running SCION VM over OpenVPN](/virtual_machine_setup/dynamic_ip/)
* [Running SCION VM with static public IP](/virtual_machine_setup/static_ip/)

## Configuring SCION infrastructure manually

The following tutorials cover how to install, configure, and run a SCION infrastructure in a step-by-step manner on a dedicated Ubuntu 16.04 host (without a VM).

### 1. Installing SCION on different platforms:

* [Installing SCION on Ubuntu 16.04 x86 machine](native_setup/ubuntu_x86_build/)
* [Installing SCION on Ubuntu MATE 16.04 - Raspberry PI](native_setup/rpi_ubuntu/)

### 2. Setting up SCION topology

* [Configuring local topology](/general_scion_configuration/local_top/)
* [Configuring AS and connecting to SCION network for devices with public static IP](/general_scion_configuration/public_ip/)
* [Configuring AS and connecting to SCION network for devices with public static IP behind a NAT](/general_scion_configuration/public_ip_nat/)
* [Configuring AS and connecting to SCION network using OpenVPN](/general_scion_configuration/vpn_setup/)
* [Configuring SCION endhost](/general_scion_configuration/setup_endhost/)

## Using SCION in projects

* [Fetching sensor readings or time stamps](/sample_projects/fetch_sensor_readings.md)
* [Fetching a camera image over the SCION network](/sample_projects/access_camera.md)
* [Running the bandwidthtester application](/sample_projects/bwtester.md)
* [Running AS Visualization](/as_visualization/running_asviz.md)
* [Browser AS Visualization](/as_visualization/browser_asviz.md)
* [Command-line AS Visualization](/as_visualization/command_asviz.md)

## SCION tips and tricks

* [Verifying the installation](/general_scion_configuration/verifying_scion_installation.md)
* [Updating gen directory](/scion_tricks/changing_gen_dir.md)
* [Updating SCION to a new version](/scion_tricks/updating_scion.md)
* [Adding Wireshark or Tshark dissector plugin](/scion_tricks/wireshark.md)

## SCIONLab specifics

* [SCION box first steps](/scionlab/scionlab.md)
