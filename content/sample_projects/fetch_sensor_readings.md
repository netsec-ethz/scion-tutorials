
# Read sensor readings over SCION network

The [sensorapp application](https://github.com/perrig/scionlab/) contains fetcher and server applications for sensor readings, using the SCION network. The application is very simple, and sends a single packet to request the information, and obtains typically a single packet in response containing the readings.

## sensorfetcher

To install sensorfetcher:
```shell
go get github.com/perrig/scionlab/sensorapp/sensorfetcher
```

The `sensorfetcher` application sends a 0-length SCION UDP packet to the `sensorserver` application to fetch the sensor readings. A string is returned containing all the sensor readings. To keep the application as simple as possible, no reliability is built in -- in case of packet loss, the user needs to abort and re-try.

To run the `sensorfetcher` application, you will need to express your local host's address as a SCION address (in the format `ISD-AS,[IPv4]:port`) and specify the address of a sensor server, for instance `1-12,[192.33.93.173]:42003`. The local ISD and AS number can be seen for instance from files in the logs directory: `br1-1006-1.log` indicates that we are in AS 1006 in ISD 1. Another way is to look at the gen directory, which in this case contains a subdirectory calles `ISD1`, which contains a subdirectory `AS1006`. The IPv4 address represents the local address the application binds to, and the local port number can be freely selected as any available port.

Some sample servers are at:

* `1-12,[192.33.93.173]:42003`
* `1-1019,[192.168.1.111]:42003`

Their readings can be fetched as follows (need to replace client address with actual client address, with an arbitrary free port):

```shell
sensorfetcher -s 1-12,[192.33.93.173]:42003 -c 1-1006,[10.0.2.15]:42001
```

### sensorserver

To install sensorserver:
```shell
go get github.com/perrig/scionlab/sensorapp/sensorserver
```

We use sensors from Tinkerforge, and the `sensorreader.py` Python application fetches the sensor values and writes them to `stdout`. The `sensorserver` application collects the readings, and serves them as a string to client requests. To start, we use the following command:

```shell
python3 ${GOPATH}/src/github.com/perrig/scionlab/sensorapp/sensorserver/sensorreader.py | sensorserver -s 1-12,[192.33.93.173]:42003 &
```

If you do not have any sensor information available, then you can use a simple time application that reports the current time on your system:

```shell
python3 ${GOPATH}/src/github.com/perrig/scionlab/sensorapp/sensorserver/timereader.py | sensorserver -s 1-12,[192.33.93.173]:42003 &
```
