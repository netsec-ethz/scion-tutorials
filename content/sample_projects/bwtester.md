
# The bwtester application

The [bandwidth testing application `bwtester`](https://github.com/perrig/scionlab/) enables a variety of bandwidth tests on the SCION network. Installation and usage are described below. Documentation of the code and protocol are described in the bwtester's [README.md](https://github.com/perrig/scionlab/blob/master/bwtester/README.md).

## bwtestclient

To install bwtestclient:
```shell
go get github.com/perrig/scionlab/bwtester/bwtestclient
```

You can test the application as follows, replacing the client address with your own address after the `-c` option (you can select any available port number for the client):

```shell
bwtestclient -s 1-6,[192.33.93.173]:30100 -c 1-1006,[10.0.2.15]:30102
```

A sample server is installed at `1-6,[192.33.93.173]:30100`.

The application supports specification of the test duration (up to 10 seconds), the packet size to be used (at least 4 bytes), and the total number of packets that will be sent. For instance, `5,100,10` specifies that 10 packets of size 100 bytes will be sent over 5 seconds. The parameters for the test in the client-to-server direction are specified with `-cs`, and the server-to-client direction with `-sc`. So for instance to send 1 Mbps for 10 seconds from the client to the server, and 10 Mbps from the server to the client, you can use this command:

```shell
bwtestclient -s 1-6,[192.33.93.173]:30100 -c 1-1006,[10.0.2.15]:30102 -cs 10,1000,1250 -sc 10,1000,12500
```

## bwtestserver

To install bwtestserver:
```shell
go get github.com/perrig/scionlab/bwtester/bwtestserver
```

The server is started as follows, where the address needs to be adjusted as for other applications:

```shell
bwtestserver -s 1-6,[192.33.93.173]:30100 &
```
