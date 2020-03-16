---
title: Sensor reading over SCION
parent: Applications
nav_order: 10
---

# Sensor reading over SCION

The [sensorapp application](https://github.com/netsec-ethz/scion-apps/) contains fetcher and server
applications for sensor readings, using the SCION network. The application is very simple: It sends
a single packet to request information and typically obtains a single packet containing the readings
in response.

## Install

To install `sensorfetcher` and `sensorserver`, run:
```shell
sudo apt install scion-apps-sensorapp
```
See [Installation](../install/pkg.html#applications) for details.


## sensorfetcher

The `sensorfetcher` application is extremely simple. It sends a 0-length SCION UDP packet to the `sensorserver` application to fetch the sensor readings. A string is returned containing all the sensor readings. No reliability is built in -- in case of packet loss, the user needs to abort and re-try.

Run the sensorfetcher application with the command

```
scion-sensorfetcher -s <server-address>
```

Sample servers are at:

* `17-ffaa:0:1102,[192.33.93.177]:42003`

## sensorserver

We use sensors from Tinkerforge, and the `sensorreader` Python application to fetch the sensor values and write them to `stdout`. The `sensorserver` application collects the readings and serves them as a string to client requests. To start, we use the following command:

```
scion-sensorreader | scion-sensorserver -p 42003
```

If you do not have any sensor information available, then you can use a simple time application that reports the current time on your system:

```
scion-timereader | scion-sensorserver -p 42003
```
