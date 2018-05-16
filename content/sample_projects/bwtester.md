
# The bwtester application

The [bandwidth testing application `bwtester`](https://github.com/perrig/scionlab/) enables a variety of bandwidth tests on the SCION network. Installation and usage are described below. Documentation of the code and protocol are described in the [bwtester README](https://github.com/perrig/scionlab/blob/master/bwtester/README.md).

## bwtestclient

To install bwtestclient and get dependencies as listed in vendor file:
```shell
go get github.com/perrig/scionlab/bwtester/bwtestclient
govendor sync
```

For govendor, see note [1].

Sample servers are installed at the following locations:

* `1-12,[192.33.93.173]:30100`
* `1-1015,[10.0.2.15]:30100`
* `1-1019,[192.168.1.111]:30100`
* `3-1034,[141.44.25.146]:30100`

You can test the application as follows, replacing the client address with your own address after the `-c` option (you can select any available port number for the client):

```shell
bwtestclient -s 1-12,[192.33.93.173]:30100 -c 1-1006,[10.0.2.15]:30102
```

The application supports specification of the test duration (up to 10 seconds), the packet size to be used (at least 4 bytes), and the total number of packets that will be sent. For instance, `5,100,10` specifies that 10 packets of size 100 bytes will be sent over 5 seconds. The parameters for the test in the client-to-server direction are specified with `-cs`, and the server-to-client direction with `-sc`. So for instance to send 1 Mbps for 10 seconds from the client to the server, and 10 Mbps from the server to the client, you can use this command:

```shell
bwtestclient -s 1-12,[192.33.93.173]:30100 -c 1-1006,[10.0.2.15]:30102 -cs 10,1000,1250 -sc 10,1000,12500
```

## bwtestserver

To install bwtestserver and get dependencies as listed in vendor file:
```shell
go get github.com/perrig/scionlab/bwtester/bwtestserver
govendor sync
```

For govendor, see note [1].

The server is started as follows, where the address needs to be adjusted as for other applications:

```shell
bwtestserver -s 1-12,[192.33.93.173]:30100 &
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
