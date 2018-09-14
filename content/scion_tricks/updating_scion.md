# Updating SCION to newest version

## Introduction

Our implementation of SCION is still under development, and ScionLab sometimes upgrades the services to use new features from new versions.
Some of these changes will break compatibility with past releases. In these cases, we will send an email notifying all the users of such changes.

!!! warning "Note"
    There is important information at the last section of this page regarding the change of addresses in SCIONLab. If you are running a dedicated machine, you probably want to read it.


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

#### ARM devices

If this is an ARM devices and we have the sources previously patched (probably they are if this is ARM), we will need to apply another patch
as explained in [Installing SCION on Ubuntu MATE 16.04 - Raspberry PI](/native_setup/rpi_ubuntu.md#step-two-apply-necessary-patches)

```shell
curl https://gist.githubusercontent.com/juagargi/f007a3a80058895d81a72651af32cb44/raw/421d8bfecdd225a3b17a18ec1c1e1bf86c436b35/arm-scionlab-update2.patch | patch -p1
```

You may find some some output informing that some hunks failed to patch. This is typically okay, as it represents portions of code that we had patched before the update and don't need to be patched again. As an example of such output we have:

```shell
patching file c/dispatcher/dispatcher.c
Hunk #1 FAILED at 837.
1 out of 1 hunk FAILED -- saving rejects to file c/dispatcher/dispatcher.c.rej
patching file c/lib/scion/address.h
patching file c/lib/scion/checksum_bench.c
Hunk #1 FAILED at 40.
Hunk #2 FAILED at 57.
2 out of 2 hunks FAILED -- saving rejects to file c/lib/scion/checksum_bench.c.rej

```

If the patch fails to apply, ensure you check out a clean version of the `scionlab` branch and patch from there.


### Rebuild SCION

If git reports that new modifications were downloaded when we rebased, it is necessary to restart scion infrastructure:

```shell
./scion.sh stop
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh clean
mv ./go/vendor/vendor.json /tmp/ && rm -r ./go/vendor/* && mv /tmp/vendor.json ./go/vendor/
./env/deps
./scion.sh build
```

Your SCION installation should be now up to date. Once SCION is built without problems, we can start the AS services again:

```shell
./scion.sh start
```

!!! hint "if running services fails"
    If building SCION succeeded but running the services did not, there might be a misconfiguration in the gen folder. If this was a manual update to support the SCIONLab update done in the summer of 2018, you will probably need to remap the identity of your AS; please keep reading to learn how to do that manually.

And the SCION services will start running.

## Last step to finish the update

After the update, the applications that are not delivered directly with SCION (e.g. `bwtester` or your own applications) will need to be rebuilt. You will have to follow the appropriate steps for each one of them reading their own documentation. E.g. `bwtester` has its own tutorial on how to build it.

There has been an important update in SCIONLab during the Summer of 2018. All the SCION addresses will be allocated in the segment ffaa:0:0/24 to accommodate a larger number of ASes than originally expected. The representation of the addresses has also changed, following the standard convention now. Details on https://github.com/scionproto/scion/wiki/ISD-and-AS-numbering .
To update manually your dedicated system, you would get the remap script and run it:
```shell
cd /tmp
wget https://raw.githubusercontent.com/netsec-ethz/scion-coord/master/scripts/remap_as_identity.sh
bash remap_as_identity.sh
```
This will complete the AS ID remapping step securely, which is necessary for the AS to work again. The remapping ensures your AS is requesting it by verifying data signed with the AS certificate.
This step should reconfigure your AS in the Coordinator and return a `gen` configuration. 

!!! hint "OpenVPN"
    If your AS was using openvpn the Coordinator should give you exactly the same IP address as the one you have running in your tunnel interface; but, if you encounter any problem regarding the border router failing to bind to a different IP address, re-download the configuration from the Coordinator and replace the openvpn `/etc/openvpn/client.conf` file with that one inside the downloaded configuration. This will ensure that the IP you obtain at the openvpn tunnel corresponds to that of the `topology.json` file.


If you experience a problem with the update, please contact us in the community mailing list. Visit the [SCION Community](https://groups.google.com/forum/#!forum/scion-community) for more details.
