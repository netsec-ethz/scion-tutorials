# Webapp Development Tips

!!! TODO
    Update & check

The `webapp` tool can be used to test several aspects of any local topology. See [updating the latest source code and starting the server](../as_visualization/webapp.md) if you have not already.

## Local Topology
Any local topology can be used with `webapp`, for example, the wide test topology:
```shell
cd $SC
./scion.sh topology -c topology/Wide.topo
```

## Development Tips
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

## Local Visualizations
You can point the browser to your test locally and view paths, run SCIONLab client apps, examine certificates and more.
![SCION Localhost Paths](../images/scion_viz.png?raw=true "SCION Localhost Paths")

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

## Related Links
* [Webapp AS Visualization](../as_visualization/webapp.md)
* [Webapp SCIONLab Apps Visualization](../as_visualization/webapp_apps.md)
* [Fetching sensor readings or time stamps](../apps/fetch_sensor_readings.md)
* [Fetching a camera image over the SCION network](../apps/access_camera.md)
* [Running the bandwidthtester application](../apps/bwtester.md)

