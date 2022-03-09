---
title: BitTorrent over SCION
parent: Applications
nav_order: 90
---

# BitTorrent over SCION

The [netsys-lab/bittorrent-over-scion](https://github.com/netsys-lab/bittorrent-over-scion) application is a BitTorrent client that uses QUIC over SCION as transport protocol.

## Install

To install `bittorrent-over-scion`, run:
```shell
go install github.com/netsys-lab/bittorrent-over-scion@v1.0.2
```

## Usage

The general usage of BitTorrent over SCION is: `bittorrent-over-scion [flags]`.

### Flags

| Flag      | Meaning                                                                                                         |
| --------- | --------------------------------------------------------------------------------------------------------------- |
| -inPath   | Source .torrent file                                                                                            |
| -file     | Source file from which the .torrent file was created, only for seeder                                           |
| -seed     | Start as seeder if set to true, otherwise start as leecher                                                      |
| -local    | The full local SCION address, of format `ISD-AS,[IP]:Port`, optional                                            |
| -peer     | The full remote SCION address, of format `ISD-AS,[IP]:Port`, only for leecher                                   |
| -outPath  | Destination to which BitTorrent writes the downloaded file, only for leecher                                    |
| -logLevel | Specifies the loglevel, can be `DEBUG`,`INFO`,`WARN`,`ERROR`, default is `INFO`, optional

At the moment, peers can be either seeders (providing a file) or leechers (requesting a file). 

### Run a seeder
The following command runs BitTorrent as a seeder:
```sh
./bittorrent-over-scion -inPath='scion-videos.torrent' -seed=true -file='scion-videos.zip' -local="19-ffaa:1:c3f,[141.44.25.148]:43000"
```

### Run a leecher
The following command runs BitTorrent as a leecher:
```sh
./bittorrent-over-scion -inPath='scion-videos.torrent' -outPath='scion-videos.zip' -peer="19-ffaa:1:c3f,[141.44.25.148]:43000" -seed=false
```

## Example torrents

We provide a [demo torrent](https://github.com/netsys-lab/bittorrent-over-scion/tree/master/demo) seeding a zip file of SCION videos under the following SCION address: `19-ffaa:1:c3f,[141.44.25.148]:43000`. To run a leecher and download this file, the respective torrent file needs to be downloaded before starting BitTorrent over SCION with he target SCION address. 

## Roadmap
We plan to extend the current BitTorrent over SCION implementation with several features to improve its usability.

We think about the following features to implement:
- [ ] Support SCION HTTP tracker
- [x] Support Dht based peer discovery
- [ ] Support magnet links
- [ ] Support multi-file torrents
- [ ] Support multiple torrents by one running instance
- [ ] Support TCP and SCION connections depending on peer information
- [ ] Add a GUI on top of the command line client
