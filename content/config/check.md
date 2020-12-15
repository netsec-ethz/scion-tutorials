---
title: Checking AS configuration
parent: Configuration
nav_order: 20
---

# Checking AS configuration


## Introduction

After [configuring your AS on the SCIONLab website](../config/create_as.html) and [installing SCION](../install/) you should now have a running SCIONLab AS. Follow the steps below to check that it is working as expected.


## Running Webapp

If you're running a VM, the simplest and recommended way of verifying a correct SCION infrastructure deployment is running the visualization tool [SCIONLab Apps Web Visualization](../apps/as_visualization/webapp.html).
This browser-based tool serves as a dashboard to your SCIONLab VM and includes various checks.


## Terminal based

For the following steps, log into the machine hosting the SCION services (with `vagrant ssh` if it is a virtual machine).
If any of the checks fail, head over to the [Troubleshooting Guide](../faq/troubleshooting.html)

### Check VPN tunnel

This only applies if you've configured your as to use an OpenVPN connection to the Attachment Point.

Check that the tunnel interface exists:

    sudo ip address show dev tun0

Check that `/etc/openvpn/client-scionlab-<attachment point ISD-AS>.conf` exists.

Check that the OpenVPN client is up:

    sudo systemctl status openvpn@client-scionlab-*

Check that the IP address in the `topology.json` file matches the IP assigned in the VPN:
Open any of the `topology.json` files and search the `interfaces` entry:

    $ grep interfaces -A10 /etc/scion/topology.json
        "interfaces": {
          "1": {
            "isd_as": "17-ffaa:0:1107",
            "underlay": {
              "public": "10.1.0.113:50000"",
              "remote": "10.1.0.1:50168"
            },
            "bandwidth": 1000,
            "link_to": "PARENT",
            "mtu": 1472
          }


In this entry, the `public` address should correspond to the local address on your tunnel interface.

Check that the VPN tunnel is working, by `ping`ing the address listed in `remote`.


### Check SCION service status

    sudo systemctl list-dependencies scionlab.target


This should show all entries as green. If there are any failed services in this list, start [troubleshooting](../faq/troubleshooting.html)

{% include alert type="note" content="
`systemd list-dependencies` lists duplicated lines on Ubuntu 16.04, due to a bug in this systemd version. You can ignore this.
" %}


If you're running a build from sources, you will need to use the developer scripts instead of `systemctl`.
Run `scion.sh status` or `supervisor/supervisor.sh status`.


### Check the border router interface status

*   Inspect the border router's log (using `sudo journactl -u scion-border-router@br-1.service`) to check that the bidirectional-forwarding detection (bfd) handshake completed and the interfaces are "active":
    
    Check that the log mentions `Transitioned from state ... to state Up`, not followed by a later `... to state Down`.

*   Alternatively, you can check the same information in metrics of the border router. For the default SCIONLab User AS setup, this is exposed on localhost, port 30401.

    ```
    $ curl -sfS localhost:30401/metrics | grep router_interface_up
    # HELP router_interface_up Either zero or one depending on whether the interface is up.
    # TYPE router_interface_up gauge
    router_interface_up{interface="1",isd_as="1-ff00:0:112",neighbor_isd_as="1-ff00:0:110"} 1
    ```


### Check that beacons are registered

An AS needs to receive path construction beacons from it's upstream provider AS(es) in order to be able to communicate.

*   Inspect the control service's log (using `sudo journalctl -u scion-control-service@cs-1.service`) to check that beacons are registered successfully.

    Check that you find entries `Registered beacons ...`, with `"count": 1` (any non-zero count).


*   Alternatively, you can check the same information in metrics of the control service, exposed by default on localhost, port 30454.

    ```
    $ curl -sfS localhost:30454/metrics | grep control_beaconing_received_beacons_total
    # HELP control_beaconing_received_beacons_total Total number of beacons received.
    # TYPE control_beaconing_received_beacons_total counter
    control_beaconing_received_beacons_total{ingress_interface="41",neighbor_isd_as="1-ff00:0:110",result="ok_updated"} 38
    ```

    Note: this returns an empty result if no beacons have been recieved.

### Ping

Ping somebody! Run `scion ping` to send an "SCMP echo request"; this is just like the `ping` command for IP.

The syntax is:

    scion ping [destination SCION address]

where a SCION address has the form `ISD-AS,IP`. An example of pinging a host in the attachment point AS in Korea would look as follows:

    $ scion ping 20-ffaa:0:1404,0.0.0.0
    Resolved local address:
      127.0.0.1
    Using path:
      Hops: [17-ffaa:1:15b 1>169 17-ffaa:0:1107 1>4 17-ffaa:0:1102 2>2 17-ffaa:0:1103 4>8 17-ffaa:0:1101 11>3 19-ffaa:0:1302 1>7 19-ffaa:0:1301 3>5 18-ffaa:0:1201 3>5 20-ffaa:0:1401 7>1 20-ffaa:0:1403 3>47 20-ffaa:0:1404] MTU: 1472, NextHop: 127.0.0.1:30042

    PING 20-ffaa:0:1404,0.0.0.0 pld=0B scion_pkt=192B
    200 bytes from 20-ffaa:0:1404,0.0.0.0: scmp_seq=0 time=383.578ms
    200 bytes from 20-ffaa:0:1404,0.0.0.0: scmp_seq=1 time=381.763ms


{% include alert type="Tip" content="
Check the topology map on the [SCIONLab homepage](https://www.scionlab.org) for an overview over the existing ASes and their addresses.
" %}

Passing this test is a condition sufficient to say that your AS works as expected. If it fails, please refer to [the troubleshooting section](../faq/troubleshooting.html).
