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

The `sensorfetcher` application sends a 0-length SCION UDP packet to the `sensorserver` application
to fetch the sensor readings. A string is returned containing all the sensor readings. To keep the
application as simple as possible, no reliability is built in -- in case of packet loss, the user
needs to abort and re-try.

Sample servers are at:

* `17-ffaa:0:1102,[192.33.93.177]:42003`
* `17-ffaa:1:13,[192.168.1.79]:42003`

Their readings can be fetched as follows:

```
scion-sensorfetcher -c 17-ffaa:1:89,[127.0.0.1]:0 -s 17-ffaa:0:1102,[192.33.93.177]:42003
```

Here, `-s` specifies the sensor server whereas `-c` specifies your machine.
The `-c` flag can be omitted if a SCION localhost address is configured. To do this, add your
SCION host to the `/etc/hosts` file. Below you can see an example:

```
# regular IPv4 hosts
# ....

# regular IPv6 hosts
# ....

# SCION hosts
17-ffaa:0:1,[192.168.1.1]                               localhost
```

## sensorserver

We use sensors from Tinkerforge, and the `sensorreader` Python application to fetch the sensor values and write them to `stdout`. The `sensorserver` application collects the readings and serves them as a string to client requests. To start, we use the following command:

```
scion-sensorreader | scion-sensorserver -s 17-ffaa:0:1102,[192.33.93.177]:42003 &
```

If you do not have any sensor information available, then you can use a simple time application that reports the current time on your system:

```
scion-timereader | scion-sensorserver -s 17-ffaa:0:1102,[192.33.93.177]:42003 &
```

In these examples,  `-s` specifies your server address. Again, it can be omitted by specifying a SCION localhost like above. The server then
uses localhost to bind to. In this case, the port can be specified with the `-p` flag.

```
scion-sensorreader | scion-sensorserver -p 42003 &
scion-timereader | scion-sensorserver -p 42003 &
```
