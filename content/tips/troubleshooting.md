# FAQ and Troubleshooting


This section answers some frequently asked questions and assists with troubleshooting for SCIONLab ASes.



## Virtual Machine
The following relate to [running a Vagrant Virtual Machine](../install/vm.md).

#### Where is the `Vagrantfile`?

Go to the details page for your AS on [SCIONLab](https://www.scionlab.org) and make sure that you've chosen the `Installation Type` "Run in a Virtual Machine".
Download the configuration tarfile by clicking the `Download Configuration` link at the bottom.
Extract the `Vagrantfile`

#### `vagrant up` fails with "Port 8000 is already in use"

The `Vagrantfile` that we create tries to forward TCP port 8000, mainly to conveniently allow running the [SCIONLab webapp](../as_visualization/webapp.md) in the VM.

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

The following relate to AS [configured to use VPN](../config/create_as.md#configure-a-scionlab-as).


#### VPN connection fails

Check the OpenVPN client log for specific information, using `sudo journalctl -e -u openvpn`.

An unspecific timeout typically indicates that your behind a firewall that blocks UDP port 1194. Please contact your network administrators to unblock this port.


#### Border Router fails to start

If you see (e.g. that your border router did not start, it is likely that the border router was trying to start before the VPN tunnel interface was up.

Simply try again;

    sudo systemctl start openvpn@client

    # ... wait a bit ... maybe check the status of the openvpn client
    sudo systemctl status openvpn@client
    ip address show dev tun0

    sudo systemctl restart scionlab.target



## SCION Services

The following are common issues or troubleshooting strategies for a SCION AS.

#### Service fails to start

Typically, this indicates a configuration error.

If you've configured to use VPN, check the [Border Router fails to start][#border-router-fails-to-start] entry in the VPN section.

Inspect the logs of the failed services to find details.
In case of multiple failures, fixing issues in the following order usually works best:
* dispatcher
* sciond
* border routers
* anything else


!!! Tip
    First clear the `/var/log/scion/` directory and restart. This often helps to find the relevant log messages quicker.


#### Not receiving beacons

You're beacon server log `/var/logs/scion/bs*.log` does not contain (recent) entries referring to `Registered beacons`.

First thing to try, is to turn it off and on again:

```
sudo systemctl restart scionlab.target
```

This may seem silly but there are (at the time of writing) certain failure modes of the border routers that are hard to diagnose and are fixed with a simple restart.


## Getting help

If your stuck, don't hesitate to [get in contact](../index.md#contact)
