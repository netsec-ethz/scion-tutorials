---
title: Troubleshooting
parent: Frequently Asked Questions
nav_order: 20
---

# Troubleshooting

## Virtual Machine
The following relate to [running a Vagrant Virtual Machine](../install/vm.html).

#### Where is the `Vagrantfile`?

Go to the details page for your AS on [SCIONLab](https://www.scionlab.org) and make sure that you've chosen the `Installation Type` "Run in a Virtual Machine".
Download the configuration tarfile by clicking the `Download Configuration` link at the bottom.
Extract the `Vagrantfile`

#### `vagrant up` fails with "Port 8000 is already in use"

The `Vagrantfile` that we create tries to forward TCP port 8000, mainly to conveniently allow running the [SCIONLab webapp](../apps/as_visualization/webapp.html) in the VM.

The error occurs because another application (could also be another VM instance!) uses this port.
Either stop the other application (find it using `netstat -natp | grep 8000`), or follow the instructions provided in the error message (see below) to change the forwarded port.

```
$ vagrant up
[ ... ]
Vagrant cannot forward the specified ports on this VM, since they
would collide with some other application that is already listening
on these ports. The forwarded port to 8000 is already in use
on the host machine.

To fix this, modify your current project's Vagrantfile to use another
port. Example, where '1234' would be replaced by a unique host port:

  config.vm.network :forwarded_port, guest: 8000, host: 1234

[ ... ]
```

#### Manage VMs without Vagrant

If you've lost track of which VMs are running or lost your `Vagrantfile`s, you can use VirtualBox directly to interact with the VMs.

* Use the `virtualbox` GUI application.
* Use `VBoxManage` from the commandline, e.g.

        $ VBoxManage list runningvms
        "SCIONLabVM-ffaa:1:15b" {1f4379a0-03e3-4efe-91e9-314e363b8d0f}
        $ VBoxManage controlvm "SCIONLabVM-ffaa:1:15b" poweroff
        $ VBoxManage list vms

## VPN

The following relate to AS [configured to use VPN](../config/create_as.html#configure-a-scionlab-as).


#### VPN connection fails

Check the OpenVPN client log for specific information, using `sudo journalctl -e -u openvpn`.

An unspecific timeout typically indicates that you are behind a firewall that blocks UDP port 1194. Please contact your network administrators to unblock this port.

#### VPN connection is unreliable

If you are experiencing seemingly random packet loss on the VPN link, are not receiving any SCION beacons, or only some,
then there might be a mismatch with the MTU on the VPN tun interface and the one supported by your physical network.

To debug this, you can (IP) ping the VPN server of the attachment point to which your are connected,
and manually discover the MTU by sending a ICMP message with a payload
(of size 1472 bytes with the `-s` flag, setting the do-no-fragment flag with `-M do`) using the following command:

```
$ ping 10.1.0.1 -W1 -c1 -s1472 -M do
```

You can lower the MTU (to 1200 bytes, or which ever MTU you discovered as working in the previous step) on the VPN interface using:

```
$ sudo ip link set dev tun0 mtu 1200
```

This only happens if MTU discovery in your network or on you system is broken, e.g. ICMP messages get dropped, so only consider this a work-around and
consider fixing your network configuration.


#### Border Router fails to start

If you see for example that your border router did not start, it is likely that the border router was trying to start before the VPN tunnel interface was up.

Simply try again;

    sudo systemctl start openvpn@client-scionlab-<attachment point ISD-AS>

    # ... wait a bit ... maybe check the status of the openvpn client
    sudo systemctl status openvpn@client-scionlab-<attachment point ISD-AS>
    ip address show dev tun0

    sudo systemctl restart scionlab.target



## SCION Services

The following are common issues or troubleshooting strategies for a SCION AS.

#### Service fails to start

Typically, this indicates a configuration error.

If you've configured to use a VPN, check the "Border Router fails to start" entry in the VPN section above.

Inspect the logs of the failed services to find details.
In case of multiple failures, fixing issues in the following order usually works best:
* dispatcher
* sciond
* border routers
* anything else

#### No paths / not receiving beacons

[If no beacons are registered](../config/check.md#check-that-beacons-are-registered), a host in a SCION AS cannot communicate.
This typically happens when the inter-AS links are not working correctly.

*   Check that the border router is (still) up

    If you are using VPN, a somewhat common issue is that the border router does not deal well with VPN tunnel interface disappearing intermittently. In particular, make sure that the VPN is started _before_ the border router.

*   Double-check that `Public IP` configured for this link in the SCIONLab website is correct

*   Check that the border router interfaces are `active` (c.f. [Check the border router interface status](../config/check.md#check-the-border-router-interface-status)).

    The mechanism for activating interfaces is based on the "bidirectional forwarding detection" protocol -- in essence, the border-routers send keep-alive messages at regular intervals.
    In a packet trace, we should see these keep-alive messages in both directions.
    It can be useful to use `wireshark` or `tcpdump` to determine whether these packets are sent and received.
    These small (~100 bytes) and regular packets are usually easy to spot in the packet trace.

*   Ensure the system clock is accurate. Drifts over 10s can cause issues. Use `ntpd` (or resort to `htpdate` if outgoing
    NTP cannot be unblocked in your infrastructure).
    
    If you find this to be the problem, but still have connectivity issues after 
    resolving it, it might be due to stale entries in the PathDB. It might take a couple of hours until all the paths are invalidated. To accelerate this, stop the SCION services (`systemctl stop scionlab.target`),
    remove the `/var/lib/scion/*path.db*` files and start the services again (`systemctl start scionlab.target`).


## Getting help

If you're stuck, don't hesitate to [get in contact](../../#contact).
