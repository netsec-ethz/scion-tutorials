
# The bwtester application

The [bandwidth testing application `bwtester`](https://github.com/netsec-ethz/scion-apps/) enables a variety of bandwidth tests on the SCION network. Installation and usage are described below. Documentation of the code and protocol are described in the [bwtester README](https://github.com/netsec-ethz/scion-apps/blob/master/bwtester/README.md).

##Install

To install `bwtestclient` and `bwtestserver` (and all [SCIONLab apps](https://github.com/netsec-ethz/scion-apps)) and get dependencies as listed in vendor file:
```shell
./deps.sh
make install
```

For govendor issues, see note [1].

## bwtestclient

!!! note
    If you are running SCION in a VM this app is already installed.

Sample servers are installed at the following locations:

* `17-ffaa:0:1102,[192.33.93.177]:30100`
* `17-ffaa:1:13,[192.168.1.79]:30100`
* `17-ffaa:1:f,[10.0.2.15]:30100`
* `19-ffaa:1:22,[141.44.25.146]:30100`

And at the attachment points:

* `17-ffaa:0:1107,[10.0.8.1]:30100`
* `18-ffaa:0:1202,[10.0.8.1]:30100`
* `19-ffaa:0:1303,[10.0.8.1]:30100`
* `20-ffaa:0:1404,[10.0.8.1]:30100`

You can test the application as follows (use `-c` to bind to a different address than localhost):

```shell
bwtestclient -s 17-ffaa:0:1102,[192.33.93.177]:30100
```

The application supports specification of the test duration (up to 10 seconds), the packet size to be used (at least 4 bytes), the total number of packets that will be sent, and the target bandwidth. For instance, `5,100,10,1600bps` specifies that 10 packets of size 100 bytes will be sent over 5 seconds, resulting in a bandwidth of 1600bps. The question mark `?` character can be used as wildcard for any of these parameters. Its value is then computed according to the other parameters. The parameters for the test in the client-to-server direction are specified with `-cs`, and the server-to-client direction with `-sc`. So for instance to send 1 Mbps for 10 seconds from the client to the server, and 10 Mbps from the server to the client, you can use this command:

```shell
bwtestclient -s 17-ffaa:0:1102,[192.33.93.177]:30100 -cs 10,1000,1250,1Mbps -sc 10,1000,12500,10Mbps
```
For more information run the application without arguments to print its usage.

## bwtestserver

The server is started as follows:

```shell
bwtestserver -p 30100 &
```

This makes the server bind to localhost and listen on port 30100. Alternatively, it can bind to any other SCION address, specified by -s:

```shell
bwtestserver -s 17-ffaa:0:1102,[192.33.93.177]:30100 &
```

***

[1] govendor: govendor is already installed by the main SCION installation with the supported version. If you don't have govendor installed, you can do so using the following steps:
```shell
mkdir $GOPATH/kardianos; cd $GOPATH/kardianos/
git clone https://github.com/kardianos/govendor.git
cd ./govendor/
git fetch; git checkout fbbc78e8d1b533dfcf81c2a4be2cec2617a926f7
go install -v
```
