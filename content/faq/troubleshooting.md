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

```none
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


{% include alert type="Tip" content="
First clear the `/var/log/scion/` directory and restart. This often helps to find the relevant log messages quicker.
" %}


#### Not receiving beacons

Log of the control service (`/var/logs/scion/cs*.log`) does not contain recent entries referring to `Registered beacons`. This typically happens when the inter-AS links are not working correctly.

*   Check that the border router is (still) up

    If you are using VPN, a somewhat common issue is that the border router does not deal well with VPN tunnel interface disappearing intermittently. In particular, make sure that the VPN is started _before_ the border router.

*   Double-check that `Public IP` configured for this link in the SCIONLab website is correct

*   Check that the border router interfaces are `active`

    Inspect the log file of control service (`/var/log/scion/cs*.log`) for messages on `Activated interface ...`.

    The mechanism for activating interfaces is based on regular keep-alive messages, sent by the control service at 1s intervals over all configured AS-interfaces.
    In a packet trace, we should see these keep-alive messages in both directions.
    It can be useful to use `wireshark` or `tcpdump` to determine whether these packets are sent and received.
    These small (~190 bytes) and regular packets are usually easy to spot in the packet trace.


## Getting help

If you're stuck, don't hesitate to [get in contact](../../#contact).
