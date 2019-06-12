
# Read sensor readings over SCION network

The [sensorapp application](https://github.com/netsec-ethz/scion-apps/) contains fetcher and server applications for sensor readings, using the SCION network. The application is very simple, and sends a single packet to request the information, and obtains typically a single packet in response, containing the readings.

## Install

To install `sensorfetcher` and `sensorserver` (and all [SCIONLab apps](https://github.com/netsec-ethz/scion-apps)) and get dependencies as listed in vendor file:
```shell
./deps.sh
make install
```

## sensorfetcher

!!! note
    If you are running SCION in a VM this app is already installed.

The `sensorfetcher` application sends a 0-length SCION UDP packet to the `sensorserver` application to fetch the sensor readings. A string is returned containing all the sensor readings. To keep the application as simple as possible, no reliability is built in -- in case of packet loss, the user needs to abort and re-try.

To run the `sensorfetcher` application, you will need to specify the address of a sensor server, for instance `17-ffaa:0:1102,[192.33.93.177]:42003`, using the `-s` flag. Per default the client binds to localhost. You can specify any other client SCION address by providing the `-c` flag.

Sample servers are at:

* `17-ffaa:0:1102,[192.33.93.177]:42003`
* `17-ffaa:1:13,[192.168.1.79]:42003`

Their readings can be fetched as follows:

```shell
sensorfetcher -s 17-ffaa:0:1102,[192.33.93.177]:42003
```

## sensorserver

We use sensors from Tinkerforge, and the `sensorreader.py` Python application fetches the sensor values and writes them to `stdout`. The `sensorserver` application collects the readings, and serves them as a string to client requests. To start, we use the following command:

```shell
python3 ${GOPATH}/src/github.com/netsec-ethz/scion-apps/sensorapp/sensorserver/sensorreader.py | sensorserver -s 17-ffaa:0:1102,[192.33.93.177]:42003 &
```

If you do not have any sensor information available, then you can use a simple time application that reports the current time on your system:

```shell
python3 ${GOPATH}/src/github.com/netsec-ethz/scion-apps/sensorapp/sensorserver/timereader.py | sensorserver -s 17-ffaa:0:1102,[192.33.93.177]:42003 &
```
