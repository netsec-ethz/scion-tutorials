# Verifying SCION installation

## Introduction

After running your SCION infrastructure, it is necessary to verify that it is working correctly.

There are several methods of doing this. Some of them are described in this post.

## Running SCION-viz

The recommended way of verifying a correct SCION infrastructure deployment is running the visualization tool [SCION-viz](../as_visualization/running_asviz.md). This user-friendly tool will display paths from your AS to different destinations, verifying the correct function of the control plane.

## Terminal based

Another approach is to directly verify AS services using the terminal. You will have to log into the machine hosting the SCION services either (with `vagrant ssh` if it is a virtual machine). If your AS is correctly configured and functioning as expected, the following checks would all pass. You can therefore choose which ones from them to check.

### Inspecting log files

The SCION log files can be accessed with the following command:

```shell
tail -f $SC/logs/bs*.DEBUG
```
In particular, the beacon server log file should contain lines like

```shell
[DEBUG] (MainThread) Successfully verified PCB ca8e78c198ca
```

If you don't find any line mentioning the successful verification of PCBs, your AS probably has issues. Please refer to [the troubleshooting section](../general_scion_configuration/troubleshooting.md).

!!! Tip
    If you are running the SCION virtual machine image, you can check the same by running:

    ```
    checkbeacons
    ```

	from any directory

### Run pingpong client

If your AS is working as expected, you should be able to use a simple data plane application that is delivered with the SCION binaries that sends a small request (_ping_) and waits for its response (_pong_). We run the `pingpong` servers in each of the four official attachment points:

* `17-ffaa:0:1107,[192.33.93.195]:40002`
* `18-ffaa:0:1202,[128.105.21.208]:40002`
* `19-ffaa:0:1303,[141.44.25.144]:40002`
* `20-ffaa:0:1404,[203.230.60.98]:40002`

You can run the `pingpong` client against any of those servers in the list above. For example, if your AS ID was `17-ffaa:1:1` and you wanted to verify `pingpong` against the attachment point in the ISD 18, you would run:

```shell
cd $SC
./bin/pingpong -local 17-ffaa:1:1,[127.0.0.1]:0 -remote 18-ffaa:0:1202,[128.105.21.208]:40002
Using path:
  Hops: [17-ffaa:1:1 1>147 17-ffaa:0:1107 1>4 17-ffaa:0:1102 3>3 17-ffaa:0:1103 4>8 17-ffaa:0:1101 5>4 18-ffaa:0:1201 6>1 18-ffaa:0:1202] Mtu: 1472
Received 13 bytes from 18-ffaa:0:1202,[128.105.21.208]:40002: seq=0 RTT=156.675ms
Received 13 bytes from 18-ffaa:0:1202,[128.105.21.208]:40002: seq=1 RTT=157.699ms
...
```

!!! Tip
	If you're running the application on a local topology, make sure to specify the correct socket using the `-sciond` flag, e.g. by adding `-sciond /run/shm/sciond/sd1-ff00_0_110.sock`. You can find the corresponding socket in the `sciond.toml` file of the endhost inside the `gen/` folder.

Passing this test is a condition sufficient to say that your AS works as expected. If it fails, please refer to [the troubleshooting section](../general_scion_configuration/troubleshooting.md).

!!! Note

If while trying to run `pingpong` you receive an error such as:
```
squic: Unable to load TLS cert/key
>      open gen-certs/tls.pem: no such file or directory
```
  Just run the following:
```shell
cd $SC
old=$(umask)
mkdir -p "gen-certs"
umask 0177
openssl genrsa -out "gen-certs/tls.key" 2048
umask "$old"
openssl req -new -x509 -key "gen-certs/tls.key" -out "gen-certs/tls.pem" -days 3650 -subj /CN=scion_def_srv
```
This would have generated the missing `gen-certs/tls.pem` and `key` files. Run again `pingpong` and you shoult not see the error about the missing certificate.
