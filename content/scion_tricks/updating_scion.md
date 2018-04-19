# Updating SCION to newest version

## Introduction

Our implementation of SCION is still under development, and ScionLab sometimes upgrades the services to use new features from new versions.
Some of these changes will break compatibility with past releases. In these cases, we will send an email notifying all the users of such changes.

## Running SCION in a VM

If you run SCION inside a VM, SCION should be kept up to date automatically. With this automatic process you don't have to worry about the updates. Still, if you are interested in knowing how we do it, read the next subsection.

### How is my SCION VM kept up to date?

You can always check that is the case by listing the running timers and looking for the one upgrading SCION:
```shell
sudo systemctl list-timers
NEXT                         LEFT        LAST                         PASSED       UNIT                         ACTIVATES
Wed 2018-04-18 16:35:24 UTC  8min left   Wed 2018-04-18 16:25:23 UTC  1min 36s ago scionupgrade.timer           scionupgrade.service
```
This indicates we have the upgrade service installed. The upgrade service is checking every day that the local git repository is up to date with the `scionlab` branch. This is done by _rebasing_ the local copy on top of the branch:
```shell
cd $SC
git fetch origin scionlab
git rebase origin/scionlab
```
We do it this to maximize the changes of successfully updating even if you had local changes done on top.
The automatic process will restart SCION if it detected that we downloaded a new version.

## Running SCION in a dedicated machine

This type of installations need manual update. If you received an email from us saying that we are going to update the infrastructure, and that the update contains _breaking changes_, you will need to follow these steps if you want to continue using SCION in ScionLab.

### Manual steps to update

We can do manually what we automatically do inside the SCION VMs, and these simple steps will update your installation of SCION.
As our SCION implementation is hosted on git, updating to the latest version is as simple as `pull`-ing changes or `rebase`-ing.
To download the newest version first navigate to SCION root directory:

```shell
cd $SC
```

then fetch newest changes from remote `scionlab` branch and rebase:

```shell
git fetch origin scionlab
git rebase origin/scionlab
```

If git reports that new modifications are downloaded, it is necessary to restart scion infrastructure:

```shell
./scion.sh stop
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```

Your SCION installation should be now up to date.

## Last step to finish the update

After the update, the applications that are not delivered directly with SCION (e.g. `bwtester` or your own applications) will need to be rebuilt. You will have to follow the appropriate steps for each one of them reading their own documentation. E.g. `bwtester` has its own tutorial on how to build it.
