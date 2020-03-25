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

Check that `/etc/openvpn/client.conf` exists.

Check that the OpenVPN client is up:

    sudo systemctl status openvpn@client


Check that the IP address in the `topology.json` file matches the IP assigned in the VPN:
Open any of the `topology.json` files and search the `Interfaces` entry:

    $ grep Interfaces -A15 /etc/scion/gen/ISD*/AS*/endhost/topology.json
        "Interfaces": {
          "1": {
            "Bandwidth": 1000,
            "ISD_AS": "17-ffaa:0:1107",
            "LinkTo": "PARENT",
            "MTU": 1472,
            "Overlay": "UDP/IPv4",
            "PublicOverlay": {
              "Addr": "10.0.8.133",
              "OverlayPort": 50000
            },
            "RemoteOverlay": {
              "Addr": "10.0.8.1",
              "OverlayPort": 50168
            }
          }

In this entry, the `PublicOverlay` address should correspond to the local address on your tunnel interface.

Finally, check that you can ping the address listed in `RemoteOverlay`.


### Check SCION service status

    sudo systemctl list-dependencies scionlab.target


This should show all entries as green. If there are any failed services in this list, start [troubleshooting](../faq/troubleshooting.html)

{% include alert type="note" content="
`systemd list-dependencies` lists duplicated lines on Ubuntu 16.04, due to a bug in this systemd version. You can ignore this.
" %}


If you're running a build from sources, you will need to use the developer scripts instead of `systemctl`.
Run `scion.sh status` or `supervisor/supervisor.sh status`.


### Inspect log files

Log files for the SCION services are located in `/var/log/scion`.

Inspect the control service's log file using e.g. `less -f /var/log/scion/cs*.log`, to check that

*   Interfaces are considered `active`:
    Check that the log mentions `Activated interface ...`, not followed by a later `interface went down`.

*   Beacons are received successfully:
    Check that you find entries `Registered beacons ...`.


### Ping

Ping somebody! Run `scmp echo` to send an "SCMP echo request"; this is just like the `ping` command for IP.

The syntax is:

    scmp echo -remote [destination SCION address]

where a SCION address has the form `ISD-AS,[IP]`. An example of pinging a host in the attachment point AS in Korea would look as follows:

    $ scmp echo -remote 20-ffaa:0:1404,[0.0.0.0]
    Using path:
      Hops: [17-ffaa:1:15b 1>169 17-ffaa:0:1107 1>4 17-ffaa:0:1102 2>2 17-ffaa:0:1103 4>8 17-ffaa:0:1101 11>3 19-ffaa:0:1302 1>7 19-ffaa:0:1301 3>5 18-ffaa:0:1201 3>5 20-ffaa:0:1401 7>1 20-ffaa:0:1403 3>47 20-ffaa:0:1404] Mtu: 1472
    200 bytes from 20-ffaa:0:1404,[0.0.0.0] scmp_seq=0 time=383.578ms
    200 bytes from 20-ffaa:0:1404,[0.0.0.0] scmp_seq=1 time=381.763ms


{% include alert type="Tip" content="
Check the topology map on the [SCIONLab homepage](https://www.scionlab.org) for an overview over the existing ASes and their addresses.
" %}

Passing this test is a condition sufficient to say that your AS works as expected. If it fails, please refer to [the troubleshooting section](../faq/troubleshooting.html).
