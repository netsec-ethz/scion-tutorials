# Running inside a VM

## Install Vagrant and Virtualbox
If you choose to run a SCIONLab virtual machine, you need to install [Vagrant](https://www.vagrantup.com/) and
[VirtualBox](https://www.virtualbox.org/). These are available for most platforms, including Linux, Windows and macOS.

On recent Debian-based systems you can install both using the following command

```shell
sudo apt-get install vagrant virtualbox
```

For other platforms, please consult the official installation instructions:

- [Install Vagrant](https://www.vagrantup.com/docs/installation/)
- [Install VirtualBox](https://www.virtualbox.org/wiki/Downloads)

## Using Vagrant to run the VM

After [creating your AS](../config/create_as.md) in the SCIONLab coordination
website, you will be able to download a tarfile with `Vagrantfile` allowing you to easily build your SCIONLab VM.

!!! note
    All the commands below need to be executed from the directory containing the Vagrantfile.

To start your VM, run

```shell
vagrant up
```

When running your VM for the first time, base Ubuntu OS will be installed together with SCIONLab packages and their
dependencies. After the installation SCIONLab services should be already up and running.

Once the `vagrant up` command returns the prompt, you can connect to your VM to
start exploring:

```shell
vagrant ssh
```

The directory containing the Vagrant file is synced with the VM where the files
will appear in the `/vagrant/` directory.
This is a convenient way to share files between your host machine and your
VM, and allows to move data both ways.

!!! note
    You can use following Vagrant commands to perform various operations with your VM
    
    * `vagrant halt` - stops the VM
    * `vagrant destroy` - restores VM to its initial state

    More information about `vagrant` commands can be found at <https://www.vagrantup.com/docs/cli>


## Configuration

As indicated above, the initial version of the configuration will automatically be provisioned.
To update the configuration in an existing VM, the workflow is the same as when running
an [installation from packages](../install/pkg.md) -- connect to your running VM
(`vagrant ssh`) and execute

```shell
sudo scionlab-config
```


## Running SCION

The SCION services are automatically started when the VM boots up.

To interact with the SCION services, you can use the same tools as when running an [installation from packages](../install/pkg.md#running-scion) --
connect to your running VM (`vagrant ssh`) and use systemd to start and stop the services
```
sudo systemctl start scionlab.target              # Start all SCION services
sudo systemctl list-dependencies scionlab.target  # Check the status
sudo systemctl stop scionlab.target               # Stop
```
