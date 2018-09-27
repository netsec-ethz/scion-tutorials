# Troubleshooting a SCIONLab installation

To reduce frustration from our users and to allow anybody to attempt to identify issues at their own pace, we have compiled this small troubleshooting guide. If it does not help you solve all the problems, and you have followed the steps, please [contact us](#contact)

The simplest case to configure, and the one we treat here, is configuring an AS within a VM, using VPN. If you are testing a different case, please try first with this one and then attempt troubleshooting your specific configuration.

## I have configured a Virtual Machine with VPN

We will first check if the Coordinator has given you the appropriate contents, then run the Virtual Machine, log into it, check that the VPN is okay, that SCION is running, has connectivity, and finally, that we are able to request paths.

#### Check tarball file and contents
After uncompressing your tar file you should see a structure like this:
```shell
$ tar xf scion_lab_user@example.com_19-ffaa_1_c.tar.gz
$ ls
user@example.com_19-ffaa_1_c  scion_lab_user@example.com_19-ffaa_1_c.tar.gz
$ cd user@example.com_19-ffaa_1_c/
$ ls 
client.conf  gen  README.md  run.sh  scion.service  scionupgrade.service  
scionupgrade.sh  scionupgrade.timer  scion-viz.service  Vagrantfile
```

In particular, if the `client.conf`, `Vagrantfile` files or the `gen` directory are missing, double check that you have configured the AS as a Virtual Machine using VPN, and click on "Update and Download SCIONLab AS Configuration". If after this you still do not see the mentioned files, please [open a bug report](#contact)

#### Run the Virtual Machine
Run the script `run.sh`. It will output many lines, but you just have to look at the last line:
```shell
$ pwd
/home/user/Downloads/user@example.com_19-ffaa_1_c/
$ ./run.sh
 .
 .
 .
ubuntu@ubuntu-xenial:~$
```
The last line is the prompt of the virtual machine itself, and seeing it means that we have successfully started the VM, and already logged into it. Skip to step [Check VPN](#check-vpn). If you don't see this and are still in the host machine, run:
```shell
$ echo $?
0
```
Anything different than `0` means a problem has occurred with running your VM. A `0` means the virtual machine is now running; skip to step [Log in the Virtual Machine](#Log-in-the-Virtual-Machine). 
Otherwise, please open your VirtualBox Manager and stop any running virtual machines to continue troubleshooting this step.
<!-- TODO: use VBoxManage -->

After stopping all running virtual machines, and being in the extracted directory, run:
```shell
$ pwd
/home/user/Downloads/user@example.com_19-ffaa_1_c/
vagrant destroy -f
 .
 .
 .
$ echo $?
0
```
This destroys your virtual machine so you can recreate it again from scratch. You can go now again to the step [Run the Virtual Machine](#Run-the-Virtual-Machine). If you had tried previously to remove all virtual machines and still see an error running `run.sh`, please [contact us](#contact)


#### Log in the Virtual Machine
After running `run.sh` you should be presented with the prompt from the virtual machine. If not, you can simply connect to it:
```shell
$ vagrant ssh
 .
 .
 .
ubuntu@ubuntu-xenial:~$
```
If you cannot get into the virtual machine, please [contact us](#contact). Otherwise go to the next step.

#### Check VPN
Our setup is using VPN to connect to the _Attachment Point_ in the SCIONLab infrastructure. We should be able to see the VPN tunnel interface when inside the VM:
```shell
ubuntu@ubuntu-xenial:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 02:b9:83:f0:a4:db brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::b9:83ff:fef0:a4db/64 scope link 
       valid_lft forever preferred_lft forever
3: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    link/none 
    inet 10.0.8.2/24 brd 10.0.9.255 scope global tun0
       valid_lft forever preferred_lft forever
```
The lines we are interested in are the last ones, that refer to the `tun0` interface. We can read them again:
```shell
3: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    link/none 
    inet 10.0.8.2/24 brd 10.0.9.255 scope global tun0
       valid_lft forever preferred_lft forever
```
It tells us that the virtual machine has a `tun0` interface with IP address `10.0.8.2`. If you don't see this `tun0` interface, [contact us](#contact).

Now, we will verify that the IP address obtained here corresponds to the one the Coordinator believes this VM should obtain. We will simply check the topology file and look for this IP address. Let's look at one topology file here:
```json
ubuntu@ubuntu-xenial:~$ cat $SC/gen/ISD61/ASffaa_1_1/cs61-ffaa_1_1-1/topology.json 
{
  "CertificateService": {
    "cs61-ffaa_1_1-1": {
      "Public": [
        {
          "Addr": "10.0.2.15",
          "L4Port": 31043
        }
      ]
    }
  },
  "BeaconService": {
    "bs61-ffaa_1_1-1": {
      "Public": [
        {
          "Addr": "10.0.2.15",
          "L4Port": 31041
        }
      ]
    }
  },
  "BorderRouters": {
    "br61-ffaa_1_1-1": {
      "Interfaces": {
        "1": {
          "ISD_AS": "61-ffaa:0:3d02",
          "Bind": {
            "Addr": "10.0.9.2",
            "L4Port": 50000
          },
          "Bandwidth": 1000,
          "InternalAddrIdx": 0,
          "LinkTo": "PARENT",
          "Overlay": "UDP/IPv4",
          "Public": {
            "Addr": "10.0.9.2",
            "L4Port": 50000
          },
          "Remote": {
            "Addr": "10.0.9.1",
            "L4Port": 60010
          },
          "MTU": 1472
        }
      },
      "InternalAddrs": [
        {
          "Public": [
            {
              "Addr": "10.0.2.15",
              "L4Port": 31042
            }
          ]
        }
      ]
    }
  },
  "ZookeeperService": {
    "1": {
      "Addr": "127.0.0.1",
      "L4Port": 2181
    }
  },
  "Overlay": "UDP/IPv4",
  "ISD_AS": "61-ffaa:1:1",
  "SibraService": {
    "sb61-ffaa_1_1-1": {
      "Public": [
        {
          "Addr": "10.0.2.15",
          "L4Port": 31045
        }
      ]
    }
  },
  "PathService": {
    "ps61-ffaa_1_1-1": {
      "Public": [
        {
          "Addr": "10.0.2.15",
          "L4Port": 31044
        }
      ]
    }
  },
  "Core": false,
  "MTU": 1472
}
```

#### SCION is running

#### SCION connectivity

#### request paths.



# Contact
Being this a troubleshooting guide, it is possible that you need to contact us to request further support. The easiest and fastest way to get support is through our [community Google Group](https://groups.google.com/forum/#!forum/scion-community). If you want to contact us for other reasons, please choose the appropriate one from this list:

* For questions, further support and general comments on SCION-related topics, visit our [SCION community Google group](https://groups.google.com/forum/#!forum/scion-community)
* For bug reports, please post them on the [scion-coord github site](https://github.com/netsec-ethz/scion-coord)
* For suggestion on these pages, please post them on the [scion-tutorials GitHub site](https://github.com/netsec-ethz/scion-tutorials)
