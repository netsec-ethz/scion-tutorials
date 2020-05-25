---
title: bat ("cURL for SCION")
parent: Applications
nav_order: 40
---

# bat ("cURL for SCION")

The [scion-apps/bat application](https://github.com/netsec-ethz/scion-apps/tree/master/bat) is a clone of [astaxie/bat](https://github.com/astaxie/bat), a cURL-like tool for sending HTTP requests to webservers and retrieve information in a human-readable format. Our fork extends `bat` with native SCION support.

## Install

To install `bat`, run:
```shell
sudo apt install scion-apps-bat
```
See [Installation](../install/pkg.html#applications) for details.

## Usage

The general usage of bat is: `bat [flags] [Method] URL [Item]`

### Flags

| Flag   | Meaning                                                                                                         |
| ------ | --------------------------------------------------------------------------------------------------------------- |
| -b     | benchmarking mode                                                                                               |
| -b.N   | number of benchmark requests to send (default 1000)                                                             |
| -b.C   | number of parallel clients in benchmark (default 100)                                                           |
| -body  | Send raw data as body                                                                                           |
| -d     | download mode with progress bar                                                                                 |
| -f     | send data `application/x-www-form-urlencoded` (default false)                                                   |
| -j     | send data `application/json` encoded (default true)                                                             |
| -p     | pretty print JSON responses (default true)                                                                      |
| -print | A (all), H (request header), B (request body), h (response header), b (response body), any combination possible |
| -v     | show version number                                                                                             |

### Method

The method can be any of the regular HTTP methods. It defaults to GET if there is no data to send and to POST otherwise.

### URL

bat accepts both, SCION addresses and hostnames in URLs. Hostnames are resolved by a RAINS request or by scanning the `/etc/hosts/` file.
The scheme defaults to HTTPS, unencrypted HTTP is not supported.

### Item

| Item               |                                  |
| ------------------ | -------------------------------- |
| key=value          | JSON/form-encoded key-value pair |
| key:value          | custom header                    |
| key=@/path/to/file | send file content as value       |

### Examples

```
scion-bat https://host1:8080/hello
scion-bat https://18-ffaa:1:2,[10.0.8.10]:8080/hello
```

## Example SCION web server

[//]: # (TODO Example servers below still built from sources. Come up with some ideas for servers to deploy in infrastructure.)

An example SCION http server application can be found in [scion-apps repository](https://github.com/netsec-ethz/scion-apps/tree/master/_examples/shttp/server/).

To build and start the servers, simply run `go run main.go` in the corresponding source code directory. Optionally specify `-p` to set a different port than 443.

This server has the following routes:

* `/hello`, replies with a greeting
* `/json`, replies with some JSON data
* `/image`, replies with an image
* `/form`, extracts POSTed form data and puts it in the reply

Some example invocations. The place-holder `<server>` is to be replaced with the address of the server started above. e.g. `17-ffaa:1:abc,[127.0.0.1]`; if you add this address to your `/etc/hosts` file you can also use a name instead.
```
scion-bat <server>/hello
scion-bat <server>/json
scion-bat -d <server>/image  # writes the downloaded image file to current working dir
scion-bat -f <server>/form foo=bar
scion-bat -b <server>/hello  # run a benchmarks
```
