---
title: Dynamic Links: Hell AP
parent: Applications
nav_order: 80
---

# Degraded Connectivity Testbed (Hell AP)

The attachment point in AS `17-ffaa:0:1113`, short name `Hell AP`, 
provides selectively degraded connectivity characteristics on the links to its ISD core.
This is useful for testing a SCION aware application in those network conditions.

{% include alert type="warning" title="Note:" content="
Only use the Hell AP if your application can handle degraded network conditions.

In any case, please reproduce application errors on any other attachment point before requesting assistance, 
or clearly mention that you were using the Hell AP.
" %}

## Link properties

The ASes `17-ffaa:0:1110`, `17-ffaa:0:1111`, `17-ffaa:0:1112`, 
and `17-ffaa:0:1113` form the `Hades` region, with degraded links.

Links between AS 1113 and AS 1112 restrict the bandwidth (200kbps, 2Mbps, 100Mbps).

Links between AS 1112 and AS 1111 increase the loss (0%, 0.1%, 100% every 2 minutes for 2 minutes).

Links between AS 1111 and AS 1110 increases the latency (0ms, 5ms, 50ms).

See the image below for a mapping of interface id to link property.

![Hades overview](/content/images/hades.png?raw=true "Hell AP and Hades region links")

## Using links with specific properties

One can then connect to a server in AS `17-ffaa:0:1102`, the parent as of AS `17-ffaa:0:1110`, 
from a UserAS connected to the Hell AP and choose the properties of the links to the server, 
e.g. 5ms latency and 0% loss with a bandwidth of 100Mbps.
For this example, one would choose the following path:
>[17-ffaa:1:userAS 1>4 17-ffaa:0:1113 3>6 17-ffaa:0:1112 1>4 17-ffaa:0:1111 2>2 17-ffaa:0:1110 4>1 17-ffaa:0:1102]

A second example would be to increase the latency by 50ms (for a RTT of over 100ms) and the loss by 0.1%. 
One would choose the following path:
>[17-ffaa:1:userAS 1>4 17-ffaa:0:1113 3>6 17-ffaa:0:1112 2>5 17-ffaa:0:1111 3>3 17-ffaa:0:1110 4>1 17-ffaa:0:1102]

Of course, you can also attach to an attachment point in a different ISD and 
run tests against a server running at the Hell AP.
There is a [bwtestserver](bwtester.html#scionlab-bandwidth-test-servers) running at `17-ffaa:0:1113,[129.132.121.185]:30100`.

## Testing

The first example can be tested with an application like the [bwtestclient](bwtester.html).
Run the bwtestclient on your UserAS against one of the bwtestservers in `17-ffaa:0:1102`:
>scion-bwtestclient -s 17-ffaa:0:1102,[129.132.121.164]:30100 -cs 101Mbps -i

The expected output is similar to the following:

(Note that the bandwidth limit of 100Mbps on the link is upper limit. Dependent on the load, 
e.g. other concurrent tests, the actual bandwidth might be lower.)

```
scion-bwtestclient -s 17-ffaa:0:1102,[129.132.121.164]:30100 -cs 101Mbps -i

Using path:
 Hops: [17-ffaa:1:userAS 1>4 17-ffaa:0:1113 3>6 17-ffaa:0:1112 1>4 17-ffaa:0:1111 2>2 17-ffaa:0:1110 4>1 17-ffaa:0:1102] MTU: 1472, NextHop: 127.0.0.1:30042
Only cs parameter set, using same values for sc

Test parameters:
clientDCAddr -> serverDCAddr 127.0.0.1:3651 -> 17-ffaa:0:1102,129.132.121.164:30101
client->server: 3 seconds, 1472 bytes, 25730 packets
server->client: 3 seconds, 1472 bytes, 25730 packets

S->C results
Attempted bandwidth: 100998826 bps / 101.00 Mbps
Achieved bandwidth: 75499861 bps / 75.50 Mbps
Loss rate: 25 %
Interarrival time variance: 24ms, average interarrival time: 0ms
Interarrival time min: 0ms, interarrival time max: 24ms

C->S results
Attempted bandwidth: 100998826 bps / 101.00 Mbps
Achieved bandwidth: 50750634 bps / 50.75 Mbps
Loss rate: 49 %
Interarrival time variance: 31ms, average interarrival time: 0ms
Interarrival time min: 0ms, interarrival time max: 31ms
```

To test the second example, you can use the `scmp echo` command and select the path from the example.

And expect an output similar to the following:
```
scmp echo -remote 17-ffaa:0:1102,[127.0.0.1] -c 1000 -interval 10ms -i

Using path:
  Hops: [17-ffaa:1:userAS 1>4 17-ffaa:0:1113 3>6 17-ffaa:0:1112 2>5 17-ffaa:0:1111 3>3 17-ffaa:0:1110 4>1 17-ffaa:0:1102] MTU: 1472, NextHop: 127.0.0.1:30042
136 bytes from 17-ffaa:0:1102,[127.0.0.1] scmp_seq=0 time=106.984ms
136 bytes from 17-ffaa:0:1102,[127.0.0.1] scmp_seq=1 time=107.376ms
...
...
136 bytes from 17-ffaa:0:1102,[127.0.0.1] scmp_seq=996 time=104.832ms
136 bytes from 17-ffaa:0:1102,[127.0.0.1] scmp_seq=997 time=106.288ms
136 bytes from 17-ffaa:0:1102,[127.0.0.1] scmp_seq=998 time=106.041ms
136 bytes from 17-ffaa:0:1102,[127.0.0.1] scmp_seq=999 time=108.878ms

--- 17-ffaa:0:1102,[127.0.0.1:0] statistics ---
1000 packets transmitted, 998 received, 1% packet loss, time 15.818845s
```

## Reproducing the setup locally

In some cases you might need a more specific setup.
For instance, the range of values you want to test your application with is not provided.
In those situations, you can setup a similar topology locally which you fully control.
You can use the following tcconfig configurations used in `Hades` as template and adapt 
the respective IAs, IPs, ports and interfaces to your topology:

`17-ffaa:0:1113`:
```
{
    "ens3": {
        "outgoing": {
            "dst-network=129.132.121.184/32, src-port=50021, dst-port=50021, protocol=ip": {
                "filter_id": "800::800",
                "rate": "200Kbps"
            },
            "dst-network=129.132.121.184/32, src-port=50022, dst-port=50022, protocol=ip": {
                "filter_id": "800::801",
                "rate": "2Mbps"
            },
            "dst-network=129.132.121.184/32, src-port=50023, dst-port=50023, protocol=ip": {
                "filter_id": "800::802",
                "rate": "100Mbps"
            }
        },
        "incoming": {
            "dst-network=129.132.121.185/32, src-port=50021, dst-port=50021, protocol=ip": {
                "filter_id": "800::800",
                "rate": "200Kbps"
            },
            "dst-network=129.132.121.185/32, src-port=50022, dst-port=50022, protocol=ip": {
                "filter_id": "800::801",
                "rate": "2Mbps"
            },
            "dst-network=129.132.121.185/32, src-port=50023, dst-port=50023, protocol=ip": {
                "filter_id": "800::802",
                "rate": "100Mbps"
            }
        }
    }
}
```

`17-ffaa:0:1112`:
```
{
    "ens3": {
        "outgoing": {
            "src-network=129.132.121.184/32, dst-network=129.132.121.183/32, src-port=50012, dst-port=50012, protocol=ip": {
                "filter_id": "800::800",
                "loss": "0.1%",
                "rate": "32Gbps"
            },
            "src-network=129.132.121.184/32, dst-network=129.132.121.183/32, src-port=50013, dst-port=50013, protocol=ip": {
                "filter_id": "800::801",
                "loss": "100%",
                "rate": "32Gbps"
            }
        },
        "incoming": {
            "src-network=129.132.121.183/32, dst-network=129.132.121.184/32, src-port=50012, dst-port=50012, protocol=ip": {
                "filter_id": "800::800",
                "loss": "0.1%",
                "rate": "32Gbps"
            },
            "src-network=129.132.121.183/32, dst-network=129.132.121.184/32, src-port=50013, dst-port=50013, protocol=ip": {
                "filter_id": "800::801",
                "loss": "100%",
                "rate": "32Gbps"
            }
        }
    }
}
```

`17-ffaa:0:1111`:
```
{
    "ens3": {
        "outgoing": {
            "src-network=129.132.121.183/32, dst-network=129.132.121.182/32, src-port=50002, dst-port=50002, protocol=ip": {
                "filter_id": "800::800",
                "delay": "5.0ms",
                "rate": "32Gbps"
            },
            "src-network=129.132.121.183/32, dst-network=129.132.121.182/32, src-port=50003, dst-port=50003, protocol=ip": {
                "filter_id": "800::801",
                "delay": "50.0ms",
                "rate": "32Gbps"
            }
        },
        "incoming": {
            "src-network=129.132.121.182/32, dst-network=129.132.121.183/32, src-port=50002, dst-port=50002, protocol=ip": {
                "filter_id": "800::800",
                "delay": "5.0ms",
                "rate": "32Gbps"
            },
            "src-network=129.132.121.182/32, dst-network=129.132.121.183/32, src-port=50003, dst-port=50003, protocol=ip": {
                "filter_id": "800::801",
                "delay": "50.0ms",
                "rate": "32Gbps"
            }
        }
    }
}
```
