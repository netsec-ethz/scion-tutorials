# Introduction

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
