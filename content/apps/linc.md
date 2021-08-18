---
title: Low-cost Industrial Network Connectivity (LINC) Gateway
parent: Applications
nav_order: 90
---

# Low-cost Industrial Network Connectivity (LINC) Gateway

The [Low-cost Industrial Network Connectivity (LINC) Gateway](https://www.netsys.ovgu.de/netsys_media/publications/LINC_SIGCOMM21.pdf) is a fork of the [SCION IP Gateway `SIG`](https://github.com/netsec-ethz/scion/tree/scionlab/go/posix-gateway) specifically tailored to provide secure and highly available connectivity for Industrial Control Systems.

## Install

LINC can be installed by building the binary from source. An Ubuntu 18.04 system is required for the build.

### Build from source

1. Install go 1.16

    See e.g. https://github.com/golang/go/wiki/Ubuntu

1. Clone the LINC fork of SCION repository:
    
    ```shell
    git clone https://github.com/tjohn327/scion.git
    git checkout sig_multipath
    ```

1. Change to posix-gateway directory:
   
   ```shell
   cd scion/go/posix-gateway
   ```

1. Build:
   
   ```shell
   go build 
   ```

1. Set network capabilities of the binary:
   
   ```shell
   sudo setcap cap_net_admin+eip posix-gateway
   ```

## Set up a LINC tunnel between two ASes

LINC tunnel between two ASes is similar to how a [SIG](../apps/remote_sig.html) tunnel is established. Make sure that the prerequisites mentioned in the [SIG tutorial](../apps/remote_sig.html#prerequisites) are satisfied.

In this tutorial, we will create a very basic setup with two LINC gateways to route between two statically configured private IP networks.
We choose the private IP range `172.16.11.0/24` for AS A and `172.16.12.0/24` for AS B.

### LINC traffic rules

Each LINC gateway requires traffic rules specified in the form of a `json` configuration file. This configuration specifies IP prefixes that should be forwarded to SIGs in remote ASes.


Note that multiple remote SIG endpoints can be specified, but in this example we have just one.

Create a `sig.json` file:
```shell
nano sig.json
```

On host A:
```json
{
    "ASes": {
        "2-ffaa:0:b": { # put your AS B here
            "Nets": [
                "172.16.12.0/24"
            ],
            "PathCount" : 2,
            "Mode": "MultiPath",
	        "PathPreference": {
                "Latency" : 0.8,
                "Bandwidth" : 0.0,
                "Jitter" : 0.2,
                "DropRate" : 0.0
	        }
        }
    },
    "ConfigVersion": 9001
}
```

On host B:
```json
{
    "ASes": {
        "1-ffaa:0:a": { # put your AS A here
            "Nets": [
                "172.16.11.0/24"
            ],
            "PathCount" : 2,
            "Mode": "MultiPath",
            "PathPreference": {
                "Latency" : 0.8,
                "Bandwidth" : 0.0,
                "Jitter" : 0.2,
                "DropRate" : 0.0
            }
        }
    },
    "ConfigVersion": 9001
}

```

Operating Modes:

* `Normal` or single path mode: when a path fails the LINC gateway switches to an alternative path as soon as the failure is detected
* `MultiPath` mode: traffic is continuously transmitted over two or more paths, If one path fails, traffic keeps flowing along the other path(s)
* `AdaptiveMultiPath` mode: follows a make-before-break approach, i.e. the LINC gateway starts duplicating traffic on an additional path as soon as the performance of the primary path drops below a threshold

Path selection preference:

The path selection preference can be specified in the corresponding section of the traffic rules configuration file. Weights can be set for each path metric such as latency, bandwidth, jitter and drop rate. The sum of the weights must be 1.0.

### LINC configuration file

In addition to the traffic rules file, LINC requires a configuration file.

Create a `sig.toml` file:

```shell
nano sig.toml
```

On host A:

```toml
[gateway]
traffic_policy_file = "/path/to/sig.json"

[tunnel]
src_ipv4 = "172.16.11.1"

[metrics]
prometheus = "127.0.0.1:30456"

[log.console]
level = "info"
```

On host B:

```toml
[gateway]
traffic_policy_file = "/path/to/sig.json"

[tunnel]
src_ipv4 = "172.16.12.1"

[metrics]
prometheus = "127.0.0.1:30456"

[log.console]
level = "info"
```

### Fake local IP addresses

To be able to send and receive IP traffic directly on the LINC host itself, the host will need an IP address in its assigned IP subnet.
Usually, the LINC will be running on a host with this address on one of its network interfaces.
We fake this by adding the relevant address to the loopback interface.

```shell
sudo ip address add 172.16.11.1 dev lo  # on host A, 172.16.12.1 on host B
```

{% include alert type="Hint" content="
This is not persistent between reboots. Make sure to add this address before starting the LINC gateway. If there is no interface with matching address, the LINC gateway may fail to add routing table entries.
" %}

### Startup

Start the LINC gateway on both hosts, by executing:

```shell
sudo ./posix-gateway --config=/path/to/sig.toml
```

{% include alert type="Warning" content="
The MTU set on the SIG's tun device is unreliable or just plain wrong. Set a conservative value of e.g. 1200 bytes on both hosts:
```shell
sudo ip link set mtu 1200 dev sig
```
" %}

### Testing the link

Ping the remote IP address to check the connectivity via LINC:

```shell
ping 172.16.12.1  # on host A, 172.16.11.1 on host B
```

This ping should successfully show echo replies. These IP packets are sent via the `sig` tun interfaces and are transported over SCION. The connectivity can be further examined using the methods described in the [SIG tutorial](https://docs.scionlab.org/content/apps/remote_sig.html#traffic).

## Remote driving over LINC

A [remote driving](https://github.com/tjohn327/self-driving-car-sim) demonstrator was developed to showcase the capabilities of LINC. It has two parts, a remote control and a car driving simulator. The remote control sends control messages to the driving simulator and simulator sends back the video feed from the front facing camera of the car. To run the remote control and the car driving simulator, they require an Ubuntu system with a graphical desktop.

Here we will setup the remote control on Host A and the car driving simulator on Host B and make them communicate over the LINC gateway.

### Setting up the remote control

Prerequisites:

The remote control requires python3 and the libraries [`opencv-python`](https://pypi.org/project/opencv-python/), [`numpy`](https://pypi.org/project/numpy/), [`pynput`](https://pypi.org/project/pynput/) and [`inputs`](https://pypi.org/project/inputs/).

On Host A:

1. Clone the repository:
   ```shell
   git clone https://github.com/tjohn327/self-driving-car-sim.git
   cd self-driving-car-sim/controller
   ```

1. Run the remote controller:
   ```shell
   python3 controller.py
   ```

### Setting up the car driving simulator

On Host B:

1. Download and extract the car driving simulator binary:
   ```shell
   wget https://github.com/tjohn327/self-driving-car-sim/releases/download/1.5/Linux.zip
   unzip Linux.zip
   cd Linux
   ```

1. Set binary as executable:
   ```shell
   chmod +x car_sim.x86_64
   ```

1. Run the car driving simulator:
   ```shell
   ./car_sim.x86_64
   ```

The remote control should now be able to control the car in the simulator. The W,A,S,D keys can be used to drive the car from the remote control. Now you can experiment with the different operating modes and path selection preferences by changing the corresponding field in the `sig.json` file.

## Additional References

* [LINC Demo Paper](https://www.netsys.ovgu.de/netsys_media/publications/LINC_SIGCOMM21.pdf)
* [Remote driving demo using LINC](https://youtu.be/774zQBsoQmA)
