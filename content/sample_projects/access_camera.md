
# Access camera images over SCION network

The [camerapp application](https://github.com/netsec-ethz/scion-apps/) contains image fetcher and server applications, using the SCION network. Documentation on the code is available in the [README.md](https://github.com/netsec-ethz/scion-apps/blob/master/camerapp/README.md).

## Install

To install `imagefetcher` and `imageserver` (and all [SCIONLab apps](https://github.com/netsec-ethz/scion-apps)) and get dependencies as listed in vendor file:
```shell
./deps.sh
make install
```

## imagefetcher

To use the image fetcher, you will need specify the address of an image server, for instance `17-ffaa:0:1102,[192.33.93.166]:42002`. Per default the client binds to localhost. You can specify any other client SCION address by providing the -c flag.

A sample image server that can be contacted by any client is set up at `17-ffaa:0:1102,[192.33.93.166]:42002`.

Images can be fetched with:
```shell
imagefetcher -s 17-ffaa:0:1102,[192.33.93.166]:42002
```

The fetched image is then saved in the local directory. A sample image is shown below:
![Sample image out of the window of office CAB E76](../images/office-20171217.jpg)

## imageserver

The `imageserver` application keeps looking for `.jpg` files in the current directory, and offers them for download to clients on the SCION network. The assumption is that the application is used in conjunction with an application that periodically writes an image to the file system. After an amount of time (currently set to 10 minutes), the image files are deleted to limit the amount of storage used.

Included is a simple `paparazzi.py` application, which reads and saves the camera image on a Raspberry Pi. The system is launched as follows:

```shell
python3 ${GOPATH}/src/github.com/netsec-ethz/scion-apps/camerapp/imageserver/paparazzi.py &
imageserver -p 42002 &
```

This makes the server bind to localhost and listen on port 42002.
Alternatively, it can bind to any other SCION address, specified by `-s`:

```shell
python3 ${GOPATH}/src/github.com/netsec-ethz/scion-apps/camerapp/imageserver/paparazzi.py &
imageserver -s 17-ffaa:0:1102,[192.33.93.166]:42002 &
```
