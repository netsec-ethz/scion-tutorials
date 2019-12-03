---
title: Webapp Development Tips
parent: AS Visualization
grand_parent: Applications
nav_order: 30
---

# Webapp Development Tips

The `webapp` tool can be used to test several aspects of any local topology.

{% include alert type="warning" content="
Most of the steps on this page are for localhost development only. If you are running a packaged install of SCIONLab, please see the [webapp package tutorial](../as_visualization/webapp.html).
" %}

## Development Setup/Run
For running `webapp` in a development environment for the SCION Infrastructure, follow the SCIONLab development install and run process at [https://github.com/netsec-ethz/scion](https://github.com/netsec-ethz/scion).

## Local Topology
Any local topology can be used with `webapp`, for example, the wide test topology:
```shell
cd $GOPATH/src/scionproto/scion
./scion.sh topology -c topology/Wide.topo
```

Then, follow these steps to install SCIONLab Apps to run `webapp` in development.

Development Install:
```shell
mkdir ~/go/src/github.com/netsec-ethz
cd ~/go/src/github.com/netsec-ethz
git clone https://github.com/netsec-ethz/scion-apps.git
```

Development Build:
Install all [SCIONLab apps](https://github.com/netsec-ethz/scion-apps) and dependencies, including `webapp`:
```shell
cd scion-apps
./deps.sh
make install
```

Development Run on Test SCION Topology:
You can alter the defaults on the command line, all of which are listed below:
```shell
webapp \
-a 127.0.0.1 \
-p 8081 \
-r . \
-srvroot $GOPATH/src/github.com/netsec-ethz/scion-apps/webapp/web \
-sabin $GOPATH/bin \
-sroot $GOPATH/src/github.com/scionproto/scion \
-sbin $GOPATH/src/github.com/scionproto/scion/bin \
-sgen $GOPATH/src/github.com/scionproto/scion/gen \
-sgenc $GOPATH/src/github.com/scionproto/scion/gen-cache \
-slogs $GOPATH/src/github.com/scionproto/scion/logs
```
or can you run `webapp` like this, which will use the defaults above:
```shell
webapp
```

Development Run on SCIONLab Topology:
```shell
webapp \
-a 0.0.0.0 \
-p 8080 \
-r ~/go/src/github.com/netsec-ethz/scion-apps/webapp/web/data \
-srvroot ~/go/src/github.com/netsec-ethz/scion-apps/webapp/web \
-sabin $GOPATH/bin \
-sroot /etc/scion \
-sbin /usr/bin \
-sgen /etc/scion/gen \
-sgenc /var/lib/scion \
-slogs /var/log/scion
```

## Development Watcher
For developing the `webapp` application itself, since it is annoying to make several changes, only to have to start and stop the web server each time, a watcher library like `go-watcher` is recommended.
```shell
go get github.com/canthefason/go-watcher
go install github.com/canthefason/go-watcher/cmd/watcher
```

After installation you can `cd` to the `webapp.go` directory and webapp will be rebuilt and rerun every time you save your source file changes, with or without command arguments.

```shell
cd $GOPATH/src/github.com/netsec-ethz/scion-apps/webapp
watcher -a 0.0.0.0 -p 8080 -r ..
```
or
```shell
cd $GOPATH/src/github.com/netsec-ethz/scion-apps/webapp
watcher
```

## Help
```
Usage of webapp:
  -a string
        Address of server host. (default "127.0.0.1")
  -p int
        Port of server host. (default 8000)
  -r string
        Root path to read/browse from, CAUTION: read-access granted from -a and -p. (default ".")
  -sabin string
        Path to execute the installed scionlab apps binaries (default "/home/ubuntu/go/bin")
  -sbin string
        Path to execute SCION bin directory of infrastructure tools (default "/home/ubuntu/go/src/github.com/scionproto/scion/bin")
  -sgen string
        Path to read SCION gen directory of infrastructure config (default "/home/ubuntu/go/src/github.com/scionproto/scion/gen")
  -sgenc string
        Path to read SCION gen-cache directory of infrastructure run-time config (default "/home/ubuntu/go/src/github.com/scionproto/scion/gen-cache")
  -slogs string
        Path to read SCION logs directory of infrastructure logging (default "/home/ubuntu/go/src/github.com/scionproto/scion/logs")
  -sroot string
        Path to read SCION root directory of infrastructure (default "/home/ubuntu/go/src/github.com/scionproto/scion")
  -srvroot string
        Path to read/write web server files. (default "/home/ubuntu/go/src/github.com/netsec-ethz/scion-apps/webapp/web")
```

## Local Visualizations
You can point the browser to your test locally and view paths, run SCIONLab client apps, examine certificates and more.
![SCION Localhost Paths](/content/images/scion_viz.png?raw=true "SCION Localhost Paths")

## Local SCIONLab App Servers
This Go web server wraps several SCION test client apps and provides an interface for any text and/or image output received. [SCIONLab Apps](http://github.com/netsec-ethz/scion-apps) are on Github.

Some functional server tests are included to test the networks without needing specific sensor or camera hardware, `bwtestserver` `sensorserver` and `imageserver`.

The server address `1-ffaa:0:112,[127.0.0.2]` is used below, but any address appropriate to your test will do. More instructions to setup the servers are [here](https://github.com/perrig/SCIONLab/blob/master/README.md). The web interface launched above can be used to run the client-side apps.

### bwtestserver
This test will setup a local server to receive requests from the bandwidth tester.
```shell
cd ${GOPATH}/src/github.com/netsec-ethz/scion-apps/bwtester/bwtestserver
go install bwtestserver.go
bwtestserver -s 1-ffaa:0:112,[127.0.0.2]:30100 -sciondFromIA &
```
Now, from your webapp browser interface running on your virtual client SCION node, you can enter both client and server addresses and ask the client for bandwidth measurements.

### sensorserver
This hardware-independent test will echo some remote machine stats from the Python script `timereader.py`, which is piped to the server for transmission to clients. On your remote SCION server node run (substituting your own address parameters):
```shell
cd ${GOPATH}/src/github.com/netsec-ethz/scion-apps/sensorapp/sensorserver
go install sensorserver.go
python3 ${GOPATH}/src/github.com/netsec-ethz/scion-apps/sensorapp/sensorserver/timereader.py | sensorserver -s 1-ffaa:0:112,[127.0.0.2]:42003 -sciondFromIA &
```
Now, from your webapp browser interface running on your virtual client SCION node, you can enter both client and server addresses and ask the client for remote sensor readings.

### imageserver
This hardware-independent test will generate an image with some remote machine stats from the Go app `local-image.go`, which will be saved locally for transmission to clients.
```shell
cd ${GOPATH}/src/github.com/netsec-ethz/scion-apps/camerapp/imageserver
go install imageserver.go
go run ${GOPATH}/src/github.com/netsec-ethz/scion-apps/webapp/tests/imgtest/imgserver/local-image.go &
imageserver -s 1-ffaa:0:112,[127.0.0.2]:42002 -sciondFromIA &
```
Now, from your webapp browser interface running on your virtual client SCION node, you can enter both client and server addresses and ask the client for the most recently generated remote image.
