# Building SCION on a Raspberry PI &ndash; Ubuntu MATE

## Introduction

The SCION infrastructure can also be run on IoT devices like a Raspberry Pi. Building SCION for a Raspberry Pi is similar to the [regular x86 build](ubuntu_x86_build), although there are a few additional steps required to make everything work.

## Prerequisites

In this tutorial we assume that you already have Raspberry PI running Ubuntu MATE. In order to install Ubuntu MATE, please follow the [installation guide](https://ubuntu-mate.org/raspberry-pi/).

!!! note "Update packages to latest version"
    It is recommended to update all packages before starting the installation process of SCION:

    ```
    sudo apt update && sudo apt upgrade
    ```

## Install necessary tools