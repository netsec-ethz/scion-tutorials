# Running inside a VM

## Install Vagrant and Virtualbox
If you choose to run a SCIONLab virtual machine, you'll only need to install
[Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/).
These are available for most platforms, including Linux, Windows and macOS.

On recent Ubuntu or Debian systems, it may be enough to run

```shell
sudo apt-get install vagrant virtualbox
```

For other platforms, please consult the official installation instructions:

- [Install Vagrant](https://www.vagrantup.com/docs/installation/)
- [Install VirtualBox](https://www.virtualbox.org/wiki/Downloads)


## Using Vagrant to run the VM

After [creating your AS](../config/create_as.md) in the SCIONLab coordination
website, you will be able to download a tarfile that includes a `Vagrantfile`
which includes all that is needed to build your SCIONLab VM.

Navigate your shell to the directory containing the Vagrantfile.
Note that all vagrant commands always need to be run in this directory!
To start your VM, run

```shell
vagrant up
```

When running your VM for the first time, this will download the Ubuntu base box
and then install all the SCION packages and their dependencies.

This will already start the services for your SCIONLab AS.

Once the `vagrant up` command returns the prompt, you can connect to your VM to
start exploring:

```shell
vagrant ssh
```

The directory containing the Vagrant file is synced with the VM where the files
will appear in the `/vagrant/` directory.
This is a convenient way to share files between your host machine and your
VM, and allows to move data both ways.

To shutdown the VM, run

```shell
vagrant halt
```

To start it back up, just type `vagrant up` again. Finally, if you want to wipe
your VM, e.g. to start fresh, run `vagrant destroy`.

More information for `vagrant` commands can be found at:
<https://www.vagrantup.com/docs/cli>


## Configuration

As indicated above, the initial version of the configuration will automatically be provisioned.
To update the configuration in an existing VM, the workflow is the same as when running
an [installation from packages](../install/pkg.md#configure); connect to your running VM
(`vagrant ssh`) and execute

```shell
sudo scionlab-config
```


## Running SCION

The SCION services are automatically started when the VM boots up.

To interact with the SCION services, you can use the same tools as when running an [installation from packages](../install/pkg.md#running-scion);
connect to your running VM (`vagrant ssh`) and use the `systemd` commands to start/stop the services
```
sudo systemctl start scionlab.target              # Start all SCION services
sudo systemctl list-dependencies scionlab.target  # Check the status
sudo systemctl stop scionlab.target               # Stop
```
