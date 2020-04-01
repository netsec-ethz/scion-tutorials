---
title: Access camera images over SCION network
parent: Applications
nav_order: 20
---

# Access camera images over SCION network

The [camerapp application](https://github.com/netsec-ethz/scion-apps/) contains image fetcher and
server applications which use the SCION network. Documentation on the code is available in the
[README.md](https://github.com/netsec-ethz/scion-apps/blob/master/camerapp/README.md).

## Install

To install `camerapp`, run:
```shell
sudo apt install scion-apps-camerapp
```
See [Installation](../install/pkg.html#applications) for details.

## imagefetcher

The `imagefetcher` application requests an image file from the server in an ad-hoc UDP-based protocol. The result is stored to a file. The name for the output file can be given on command line, otherwise it uses the filename given by the server.

Run the image fetcher application with the command

```
scion-imagefetcher -s <server-address> [-output <filename.jpg>]
```

Sample servers are at:

* `17-ffaa:0:1102,[192.33.93.166]:42002`

{% include alert type="Tip" content="
`imagefetcher` is also available via the [webapp](../apps/as_visualization/webapp_apps.html).
" %}

## imageserver

The `imageserver` application keeps looking for `.jpg` files in the current directory, and offers them for download to clients on the SCION network. The assumption is that the application is used in conjunction with an application that periodically writes an image to the file system. After an amount of time (currently set to 10 minutes), the image files are deleted to limit the amount of storage used.

Run the image server application with the command

```
scion-imageserver -p <port>
```
