# Updating SCION to newest version

## Introduction

Our implementation of SCION is still under development, and ScionLab sometimes upgrades the services to use new features from new versions.
Some of these changes will break compatibility with past releases. In these cases, we will send an email notifying all the users of such changes.


## Running SCION in a VM

If you run SCION inside a VM, SCION should be kept up to date automatically. With this automatic process you don't have to worry about the updates. Still, if you are interested in knowing how we do it, read the next subsection.

### How is my SCION VM kept up to date?

SCIONLab runs a packaged version of SCION. In the VMs we automatically install a file pointing to our `apt` repository in `/etc/apt/sources.list.d/scionlab.list`. The content is just one line:
```
deb [trusted=yes] https://packages.netsec.inf.ethz.ch/debian all main
```
There is also an entry in root's `crontab` that will update any `scionlab` package. You can check that running:
```shell
$ sudo crontab -l

00 07 * * * apt-get update; apt-get install -y --only-upgrade scionlab
```


## Running SCION in a dedicated machine

This type of installations need a manual trigger of the update. If you received an email from us saying that we are going to update the infrastructure, and that the update contains _breaking changes_, you will need to follow these steps if you want to continue using SCION in ScionLab.

If your dedicated system is already using packages, it would be enough to run:
```shell
sudo apt-get update
sudo apt-get install --only-upgrade scionlab
```

If you are still running the old style system, [migrate to packages](#migrate-to-packages). You can check if you are still running the old style upgrade by checking your dedicated system doesn't have the mentioned sources file:
```shell
$ ls /etc/apt/sources.list.d/scionlab.list
cat: /etc/apt/sources.list.d/scionlab.list: No such file or directory
```


### Migrate to packages

We will install the packages by running:
```shell
echo "deb [trusted=yes] https://packages.netsec.inf.ethz.ch/debian all main" | sudo tee /etc/apt/sources.list.d/scionlab.list
sudo apt-get update
sudo apt-get install scionlab
```

If you installed zookeeper because it used to be a SCION dependency, you can remove it now `sudo apt-get purge -y zookeeper`.

Lastly, check that no old scion services are running or installed. While running these steps some of the commands may fail, if there was no such service installed in your dedicated system; but that is alright.
```shell
sudo systemctl disable --now scion.service
sudo systemctl disable --now scionupgrade.timer
sudo systemctl disable --now scionupgrade.service
sudo rm /etc/systemd/system/scion.service
sudo rm /etc/systemd/system/scionupgrade.timer
sudo rm /etc/systemd/system/scionupgrade.service
```

## Developers

As a developer, you probably don't need or want systemd to run and manage the SCION services for you. Stop it by running `sudo systemctl stop scionlab.target` and continue using `supervisord` as before.
If you want to update your git clone of SCION, just run `git pull` on your `scionlab` branch, like always. The build process now relies on `bazel`: read more in [scionproto README](https://github.com/scionproto/scion/blob/master/README.md).


## Contact us

If you experience a problem with the update, please contact us in the community mailing list. Visit the [SCION Community mailing list](https://lists.inf.ethz.ch/mailman/listinfo/scion) for more details.
