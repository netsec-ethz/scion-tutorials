# Building SCION for Raspberry PI - Ubuntu MATE

## Introduction

SCION infrastructure can be run on IoT devices like Raspberry PI. Building SCION for Raspberry PI is similar to regular x86 build, although there are few additional steps required to make everything work.

## Prerequisites

In this tutorial we assume that you already have Raspberry PI running Ubuntu MATE. In order to install Ubuntu MATE, please follow [installation guide](https://ubuntu-mate.org/raspberry-pi/)

!!! note "Update packages to latest version"
    It is recommended to update packages in your package

    ```
    sudo apt update && sudo apt upgrade
    ```

## Installing necessary tools