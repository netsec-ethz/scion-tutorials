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
See [Installation](../install/pkg.md#applications) for details.

## imagefetcher

To use the image fetcher, you will need to specify the address of an image server. A sample image server that can be contacted by any client is set up at `17-ffaa:0:1102,[192.33.93.166]:42002`.

Images can be fetched like this:
```
scion-imagefetcher -c 17-ffaa:1:89,[127.0.0.1]:0 -s 17-ffaa:0:1102,[192.33.93.166]:42002
```

Here, `-s` specifies the image server whereas `-c` specifies your machine.
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

{% include alert type="Tip" content="
`imagefetcher` is also available via the [webapp](../as_visualization/webapp_apps.md).
" %}

## imageserver

The `imageserver` application keeps looking for `.jpg` files in the current directory, and offers them for download to clients on the SCION network. The assumption is that the application is used in conjunction with an application that periodically writes an image to the file system. After an amount of time (currently set to 10 minutes), the image files are deleted to limit the amount of storage used.

The image server is launched as follows:

```
scion-imageserver -s 17-ffaa:1:89,[127.0.0.1]:40002 &
```
Here `-s` specifies your server address. Again, it can be omitted by specifying a SCION localhost like above. The server then
uses localhost to bind to. In this case, the port can be specified with the `-p` flag.

```
scion-imageserver -p 40002 &
```
