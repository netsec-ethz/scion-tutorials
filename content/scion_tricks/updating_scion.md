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
We do this to maximize the chances of successfully updating even if you had local changes done on top.
The automatic process will restart SCION if it detected that we downloaded a new version.

## Running SCION in a dedicated machine

This type of installations need manual trigger of the update. If you received an email from us saying that we are going to update the infrastructure, and that the update contains _breaking changes_, you will need to follow these steps if you want to continue using SCION in ScionLab.

There are essentially two possibilities for dedicated systems:

1. [Run a script to update your dedicated systems](#script-to-update)
2. [Completely manually do the steps (experts only)](#manual-steps-to-update-experts)

We recommend the first approach because its simplicity.


### Script to update

We offer the possibility of a simplified way to update your system. You would have to run the following steps in each of your dedicated systems:

```shell
cd /tmp
wget https://raw.githubusercontent.com/netsec-ethz/scion-coord/master/scion_upgrade_script.sh -O upgrade.sh
bash upgrade.sh -m
```

The `-m` parameter is important. This should be enough to update your AS.
If it fails, ensure that you have in `$SC` a git remote named `origin` pointing to our repository at https://github.com/netsec-ethz/netsec-scion.git . You can check this by running `cd $SC; git remote -v`


### Manual steps to update (experts)

If you choose to perform the update yourself, the steps to manually update your AS involve:

- Update the source code of the SCION implementation.
- Rebuild SCION services.
- Get an updated configuration from the Coordinator


#### Update the source code
Stash all uncommited changes before doing this procedure.
```shell
cd $SC
./scion.sh stop
git stash
git fetch origin
git reset --hard origin/scionlab
```

#### Rebuild SCION Services
```shell
cd $SC
sudo apt-get purge parallel
./scion.sh clean
./env/deps
./scion.sh build
```

#### Get updated configuration
```shell
cd /tmp
wget https://raw.githubusercontent.com/netsec-ethz/scion-coord/master/scripts/check_as_config.sh -O check_as_config.sh
bash check_as_config.sh -f
```


## Last step to finish the update
Your SCION installation should be now up to date. If SCION is built without problems, we will reload the configuration and then start SCION:

```shell
./supervisor/supervisor.sh reload
rm ./gen-cache/*
./scion.sh start nobuild
```

## SCION Applications

SCION services should be running now.

After the update, the applications that are not delivered directly with SCION (e.g. `bwtester` or your own applications) will need to be rebuilt. You will have to follow the appropriate steps for each one of them reading their own documentation. E.g. `bwtester` has its own tutorial on how to build it.

## Contact us

If you experience a problem with the update, please contact us in the community mailing list. Visit the [SCION Community](https://groups.google.com/forum/#!forum/scion-community) for more details.
