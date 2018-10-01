# Troubleshooting a SCIONLab installation

To reduce frustration from our users and to allow anybody to attempt to identify issues at their own pace, we have compiled this small troubleshooting guide. If it does not help you solve all the problems, and you have followed the steps, please [contact us](#contact)

The simplest case to configure, and the one we treat here, is configuring an AS within a VM, using VPN. If you are testing a different case, please try first with this one and then attempt troubleshooting your specific configuration.

## I have configured a Virtual Machine with VPN
We will first check if the Coordinator has given you the appropriate contents, then run the Virtual Machine, log into it, check that the VPN is okay, that SCION is running, has connectivity, and finally, that we are able to request paths.

### Check Tarball File and Contents
After uncompressing your tar file you should see a structure like below. The `17-ffaa_1_64` is the AS ID we are configuring, in your case it will be another one:
```shell
$ tar xf scion_lab_user@example.com_17-ffaa_1_64.tar.gz
$ ls
user@example.com_17-ffaa_1_64  scion_lab_user@example.com_17-ffaa_1_64.tar.gz
$ cd user@example.com_17-ffaa_1_64/
$ pwd
/home/user/Downloads/user@example.com_17-ffaa_1_64/
$ ls 
client.conf  gen  README.md  run.sh  scion.service  scionupgrade.service  
scionupgrade.sh  scionupgrade.timer  scion-viz.service  Vagrantfile
```

The `pwd` command indicates in which directory the terminal prompt is working (the _working directory_). In your case it will be something different than `/home/user/Downloads/user@example.com_17-ffaa_1_64/` but ending in a directory that represents your user and the AS ID you configured (`user@example.com_17-ffaa_1_64`).
Pay attention to the contents of the directory. In particular, if the `client.conf`, `Vagrantfile` files or the `gen` directory are missing, double check that you have configured the AS as a Virtual Machine using VPN, and click on "Update and Download SCIONLab AS Configuration". If after this you still do not see the mentioned files, please [open a bug report](#contact)

### Run the Virtual Machine
The script `run.sh` will check for you the versions of Virtualbox and Vagrant installed in your system, and install them if needed. Although not necessary, you can perform this step manually if you wish to:
```shell
$ vagrant --version
Vagrant 2.1.1
```
If your version is lower than `1.9` or not installed at all, please install it:
```shell
$ sudo apt-get install --only-upgrade vagrant
```

We recommend `virtualbox` version `5.2` with your Ubuntu 16.04 installation. Since it is not available in the default repositories, you would have to uninstall your current virtualbox version (if any) and then install the recommended one:
```shell
$ wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
$ wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
$ sudo sh -c 'echo "deb http://download.virtualbox.org/virtualbox/debian xenial contrib" >> /etc/apt/sources.list.d/virtualbox.list'
$ sudo apt remove virtualbox virtualbox-5.1
$ sudo apt-get --no-remove --yes install virtualbox-5.2
```

To automatically do all this and start your virtual machine, run the script `run.sh`. It will output many lines, but you just have to look at the last line:
```shell
$ pwd
/home/user/Downloads/user@example.com_17-ffaa_1_64/
$ ./run.sh
 .
 .
 .
ubuntu@ubuntu-xenial:~$
```
The last line is the prompt of the virtual machine itself, and seeing it means that we have successfully started the VM, and already logged into it. Skip to step [Check VPN](#check-vpn).

If you don't see this and are still in the host machine, run:
```shell
$ echo $?
0
```

Anything different than `0` means a problem has occurred with running your VM. A `0` means the virtual machine is now running; skip to step [Log in the Virtual Machine](#Log-in-the-Virtual-Machine). 

If you experience an error mentioning that Vagrant cannot forward the specified port, please shut down all of your running virtual machines. Keep reading for more information. The error would look similar to this:
```shell
 $ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Checking if box 'scion/ubuntu-16.04-64-scion' is up to date...
==> default: Clearing any previously set forwarded ports...
Vagrant cannot forward the specified ports on this VM, since they
would collide with some other application that is already listening
on these ports. The forwarded port to 8000 is already in use
on the host machine.

To fix this, modify your current project's Vagrantfile to use another
port. Example, where '1234' would be replaced by a unique host port:

  config.vm.network :forwarded_port, guest: 8000, host: 1234

Sometimes, Vagrant will attempt to auto-correct this for you. In this
case, Vagrant was unable to. This is usually because the guest machine
is in a state which doesn't allow modifying port forwarding. You could
try 'vagrant reload' (equivalent of running a halt followed by an up)
so vagrant can attempt to auto-correct this upon booting. Be warned
that any unsaved work might be lost.
```

To proceed, please open your VirtualBox Manager and stop any running virtual machines to continue troubleshooting this step.

!!! tip
    Find out how many virtual machines you have running by typing in the terminal `VBoxManage list runningvms`. If you are in doubt on how to stop the running virtual machines, you can always reboot your host machine to ensure none are running.

After stopping all running virtual machines, and being in the extracted directory, 
<a name="destroy"></a>
remove your virtual machine completely:
```shell
$ VBoxManage list runningvms
# VBoxManage prints nothing because no VM running
$ pwd
/home/user/Downloads/user@example.com_17-ffaa_1_64/
vagrant destroy -f
 .
 .
 .
$ echo $?
0
```
This destroys your virtual machine so you can recreate it again from scratch. You can go now again to the step [Run the Virtual Machine](#Run-the-Virtual-Machine). If you had tried previously to remove all virtual machines and still see an error running `run.sh`, please [contact us](#contact)


### Log in the Virtual Machine
After running `run.sh` you should be presented with the prompt from the virtual machine. If not, you can simply connect to it:
```shell
$ vagrant ssh
 .
 .
 .
ubuntu@ubuntu-xenial:~$
```
If you cannot get into the virtual machine, please [contact us](#contact). To be sure we will check now the free space on the virtual machine virtual disk:
```shell
ubuntu@ubuntu-xenial:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            487M     0  487M   0% /dev
tmpfs           100M  6.8M   93M   7% /run
/dev/sda1       9.7G  4.9G  4.8G  51% /
tmpfs           497M   99M  398M  20% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           497M     0  497M   0% /sys/fs/cgroup
vagrant         451G   51G  401G  12% /vagrant
tmpfs           100M     0  100M   0% /run/user/1000
```
Interesting for us is the line `/dev/sda1       9.7G  4.9G  4.8G  51% /` because it refers to `/` (last column). Look for a similar entry from your output and check that on the `Avail` column you have at least `2G` (2 gigabytes). Otherwise [destroy your virtual machine](#destroy) and [create a new one](#run-the-virtual-machine).

Your host machine should have enough memory to launch a virtual machine requesting two gigabytes of free memory (we configure SCIONLab virtual machines to use two gigabytes of memory). Ensure that you have enough free memory in your host and that in the VM you see the expected free memory amount:
```shell
ubuntu@ubuntu-xenial:~$ free -h
              total        used        free      shared  buff/cache   available
Mem:           2.0G        271M        1.2G        3.1M        499M        1.5G
Swap:            0B          0B          0B
```
The line `Mem:           2.0G        271M        1.2G        3.1M        499M        1.5G` tells us that there are two gigabytes of memory in total. If you don't have two gigabytes (and did not edit your `Vagrantfile` yourself), please [contact us](#contact).

### Check VPN
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
    inet 10.0.8.8/24 brd 10.0.8.255 scope global tun0
       valid_lft forever preferred_lft forever
```
The lines we are interested in are the last ones, that refer to the `tun0` interface. We can read them again:
```shell
3: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 100
    link/none 
    inet 10.0.8.8/24 brd 10.0.8.255 scope global tun0
       valid_lft forever preferred_lft forever
```
It tells us that the virtual machine has a `tun0` interface with IP address `10.0.8.8`. If you don't see this `tun0` interface, you are probably behind a firewall that does not allow UDP traffic on port `1194`. Please check your `/var/log/syslog` to find out if there had been a timeout while trying to establish the `openvpn` connection (search for `ovpn-client` in the `/var/log/syslog` file). If you find out that the `tun0` interface was not brought up because timeouts between your client and the VPN server, it is an indication that a firewall is filtering the traffic: please contact your IT service to add an exception for your machine and port `1194`.
Also, you can [contact us](#contact) to find out if we can do something about that.

Now, we will verify that the IP address obtained here corresponds to the one the Coordinator believes this VM should obtain. We will simply check the topology file and look for this IP address. Let's look at one topology file. For completeness we show the whole file here, but we only need to find the string containing the `tun0` address; from our previous example we look for `10.0.8.8`. We find it inside the `BourderRouters` block as both the `Public` and `Bind` address for the only border router interface we have, as expected.

```json
ubuntu@ubuntu-xenial:~$ cat $SC/gen/ISD17/ASffaa_1_64/cs17-ffaa_1_64-1/topology.json 
{
  "PathService": {
    "ps17-ffaa_1_64-1": {
      "Public": [
        {
          "L4Port": 31044,
          "Addr": "10.0.2.15"
        }
      ]
    }
  },
  "Core": false,
  "CertificateService": {
    "cs17-ffaa_1_64-1": {
      "Public": [
        {
          "L4Port": 31043,
          "Addr": "10.0.2.15"
        }
      ]
    }
  },
  "BeaconService": {
    "bs17-ffaa_1_64-1": {
      "Public": [
        {
          "L4Port": 31041,
          "Addr": "10.0.2.15"
        }
      ]
    }
  },
  "ZookeeperService": {
    "1": {
      "L4Port": 2181,
      "Addr": "127.0.0.1"
    }
  },
  "Overlay": "UDP/IPv4",
  "MTU": 1472,
  "SibraService": {
    "sb17-ffaa_1_64-1": {
      "Public": [
        {
          "L4Port": 31045,
          "Addr": "10.0.2.15"
        }
      ]
    }
  },
  "BorderRouters": {
    "br17-ffaa_1_64-1": {
      "Interfaces": {
        "1": {
          "Bandwidth": 1000,
          "Remote": {
            "L4Port": 50015,
            "Addr": "10.0.8.1"
          },
          "InternalAddrIdx": 0,
          "LinkTo": "PARENT",
          "Bind": {
            "L4Port": 50000,
            "Addr": "10.0.8.8"
          },
          "MTU": 1472,
          "Overlay": "UDP/IPv4",
          "Public": {
            "L4Port": 50000,
            "Addr": "10.0.8.8"
          },
          "ISD_AS": "17-ffaa:0:1107"
        }
      },
      "InternalAddrs": [
        {
          "Public": [
            {
              "L4Port": 31042,
              "Addr": "10.0.2.15"
            }
          ]
        }
      ]
    }
  },
  "ISD_AS": "17-ffaa:1:64"
}
```
If you find your `tun0` IP address to be the same as in the `Bind` and `Public` parts of the `BorderRouter` blocks, please continue to step [Check SCION is running](#check-scion-is-running). If you did not find the `tun0` IP address in your topology file, we will destroy the existing virtual machine and remove its settings by first logging out of it and then running the steps described in the snippet [vagrant destroy](#destroy). After destroying the virtual machine, we can delete its configuration:

```shell
$ vagrant destroy -f
 .
 .
 .
$ cd ..
$ pwd
/home/user/Downloads/
$ rm -r user@example.com_17-ffaa_1_64
```

Now check [in the Coordinator webpage](https://www.scionlab.org) that your AS is correctly attached to your AP of choice, and that you are using the right tarball file. If in doubt, you can always click on _Re-download my SCIONLab AS Configuration_ to get it again. _Re-download_ does not configure the AS, but returns the latest configuration the Coordinator has for it. Wait 15 minutes (the reason being sometimes the attachment point needs 15 minutes to process your request). You should have received an email stating the success of your request. In the hopefully successful state, start again from [the checking tarbal step](#check-tarball-file-and-contents). If after waiting these 15 minutes you did not receive the success email, or you received it but still don't see the same IP address in the `tun0` interface as in the topology file, [contact us](#contact).

### Check SCION is running
We are goint to check now that all SCION processes are running. Once logged in the VM, run this:
```shell
ubuntu@ubuntu-xenial:~$ cd $SC
ubuntu@ubuntu-xenial:~/go/src/github.com/scionproto/scion$ ./scion.sh status
ubuntu@ubuntu-xenial:~/go/src/github.com/scionproto/scion$ 
```
As a general rule, `scion.sh status` should not print any messages. Output from that command indicates problems with the SCION services. If your execution returned a message, there is probably a problem. Stop and start SCION again to retry just once more:
```shell
ubuntu@ubuntu-xenial:~/go/src/github.com/scionproto/scion$ ./scion.sh stop
Terminating this run of the SCION infrastructure
dispatcher: stopped
as17-ffaa_1_64:sd17-ffaa_1_64: stopped
as17-ffaa_1_64:br17-ffaa_1_64-1: stopped
as17-ffaa_1_64:cs17-ffaa_1_64-1: stopped
as17-ffaa_1_64:ps17-ffaa_1_64-1: stopped
as17-ffaa_1_64:bs17-ffaa_1_64-1: stopped
ubuntu@ubuntu-xenial:~/go/src/github.com/scionproto/scion$ ./scion.sh start
Compiling...
Running the network...
dispatcher: started
as17-ffaa_1_64:sd17-ffaa_1_64: started
as17-ffaa_1_64:br17-ffaa_1_64-1: started
as17-ffaa_1_64:bs17-ffaa_1_64-1: started
as17-ffaa_1_64:cs17-ffaa_1_64-1: started
as17-ffaa_1_64:ps17-ffaa_1_64-1: started
ubuntu@ubuntu-xenial:~/go/src/github.com/scionproto/scion$ ./scion.sh status
ubuntu@ubuntu-xenial:~/go/src/github.com/scionproto/scion$ 
```
If now the `scion.sh status` command still prints messages (wether the same or different ones than before), please [contact us](#contact), copying that output on the message.
You should at this stage check the AS ID in the virtual machine, and ensure it corresponds to the one you expect. In our case, we expect to see the AS ID `17-ffaa_1_64`:
```shell
ubuntu@ubuntu-xenial:~/go/src/github.com/scionproto/scion$ cat $SC/gen/ia
17-ffaa_1_64ubuntu@ubuntu-xenial:~/go/src/github.com/scionproto/scion$
```
The output indicates `17-ffaa_1_64`, which is okay, because the `ia` file replaces `:` with `_`. If not, you probably ran the virtual machine from the wrong uncompressed tarball. Check again starting in step [Check Tarball File and Contents](#check-tarball-file-and-contents). Otherwise, [contact us](#contact).

The last step is only for sanity: SCION services communicate among themselves using socket files. The following `default.sock` files should be present in your VM, as shown below:

```shell
ubuntu@ubuntu-xenial:~$ ll /run/shm/
total 0
drwxrwxrwt  5 root      root       100 Sep 27 15:53 ./
drwxr-xr-x 16 root      root      3620 Sep 27 15:53 ../
drwxr-xr-x  3 ubuntu    ubuntu      80 Sep 28 09:32 dispatcher/
drwxr-xr-x  3 zookeeper zookeeper   60 Sep 27 15:53 host-zk/
drwxr-xr-x  2 ubuntu    ubuntu      60 Sep 28 09:32 sciond/
ubuntu@ubuntu-xenial:~$ ll /run/shm/sciond/
total 0
drwxr-xr-x 2 ubuntu ubuntu  60 Sep 28 09:32 ./
drwxrwxrwt 5 root   root   100 Sep 27 15:53 ../
srwxr-xr-x 1 ubuntu ubuntu   0 Sep 28 09:32 default.sock=
ubuntu@ubuntu-xenial:~$ ll /run/shm/dispatcher/
total 0
drwxr-xr-x 3 ubuntu ubuntu  80 Sep 28 09:32 ./
drwxrwxrwt 5 root   root   100 Sep 27 15:53 ../
srwxr-xr-x 1 ubuntu ubuntu   0 Sep 28 09:32 default.sock=
drwxr-xr-x 2 ubuntu ubuntu  60 Sep 28 09:32 lwip/
ubuntu@ubuntu-xenial:~$ 
```
If there are no `sciond` or `dispatcher` directories, or if they don't contain a `default.sock` file, please [contact us](#contact).

### Check Request Paths.
Every AS should be able to reach any other AS if there exist at least a path. We will start by checking that your VM running the services for your AS is able to obtain paths to some well-known ASes:
```shell
ubuntu@ubuntu-xenial:~$ cd $SC
ubuntu@ubuntu-xenial:~/go/src/github.com/scionproto/scion$ ./bin/showpaths -srcIA 17-ffaa:1:64 -dstIA 17-ffaa:0:1101
INFO[09-28|12:47:16] Started                                  id=595ed707 goroutine=dispatcher_bck
Available paths to 17-ffaa:0:1101
[ 0] Hops: [17-ffaa:1:64 1>16 17-ffaa:0:1107 1>4 17-ffaa:0:1102 2>2 17-ffaa:0:1103 4>8 17-ffaa:0:1101] Mtu: 1472
[ 1] Hops: [17-ffaa:1:64 1>16 17-ffaa:0:1107 1>4 17-ffaa:0:1102 3>3 17-ffaa:0:1103 4>8 17-ffaa:0:1101] Mtu: 1472
```
Replace `17-ffaa:1:64` with the IA of your AS (the normal IA contains `:`, not `_`). You can check paths to reach `17-ffaa:0:1101` because it is an important AS and should always be present in the network. There must always be at least one path. 

If you don't see paths, go to [step Check SCION Connectivity](#check-scion-connectivity) and repeat this check about getting paths afterwards.

On the other hand, if you were able to obtain paths, using the control plane to send _echo_ messages should work:
```shell
ubuntu@ubuntu-xenial:~$ cd $SC
ubuntu@ubuntu-xenial:~/go/src/github.com/scionproto/scion$ ./bin/scmp echo -local 17-ffaa:1:64,[127.0.0.1] -remote 17-ffaa:0:1101,[127.0.0.1]
INFO[09-28|12:54:01] Started                                  id=1ead82ed goroutine=dispatcher_bck
Using path:
  Hops: [17-ffaa:1:64 1>16 17-ffaa:0:1107 1>4 17-ffaa:0:1102 2>2 17-ffaa:0:1103 4>8 17-ffaa:0:1101] Mtu: 1472
120 bytes from 17-ffaa:0:1101,[127.0.0.1] scmp_seq=0 time=10.765ms
120 bytes from 17-ffaa:0:1101,[127.0.0.1] scmp_seq=1 time=10.973ms
120 bytes from 17-ffaa:0:1101,[127.0.0.1] scmp_seq=2 time=10.164ms
^C
--- 17-ffaa:0:1101,[127.0.0.1] statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2.382946s
```
Replace `17-ffaa:1:64` with the IA of your AS. If the echo does not work but you still saw paths to reach the AS from your own, please [contact us](#contact). If you saw this step working, congratulations, you have fixed the issues preventing your AS to function, and now you should be able to use your VM as you wish. If you think that the problems you faced could have been prevented by doing something in a different way, please [get in touch with us](#contact) with your suggestions.

### Check SCION Connectivity
So in terms of the VM structure, IDs and processes everything seems to be alright. But our applications still don't work. We need to go a bit more low level and find out what the problem is. In this check we will test if the connection between the VM and the attachment point seems okay or not.

Because the tunnel interface `tun0` is established (double check you still see the `tun0` interface when running `ip a`), we should be able to reach the other end. Recalling the topology file we showed in the [Check VPN step](#check-vpn), we look at the `BorderRouter` block, `Remote` part:
```json
          "Remote": {
            "Addr": "10.0.8.1",
            "L4Port": 60010
          },
```
It indicates the other end's IP address is `10.0.8.1`. It is typically our own VPN IP address (in our examples`10.0.8.8`) with a `1` replacing the number after the last dot `.`, but we are now sure this is the address of the attachment point. We should be able to reach it:
```shell
ubuntu@ubuntu-xenial:~$ ping 10.0.8.1
PING 10.0.8.1 (10.0.8.1) 56(84) bytes of data.
64 bytes from 10.0.8.1: icmp_seq=1 ttl=64 time=0.706 ms
64 bytes from 10.0.8.1: icmp_seq=2 ttl=64 time=0.829 ms
^C
--- 10.0.8.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1038ms
rtt min/avg/max/mdev = 0.706/0.767/0.829/0.067 ms
```
If your `ping` command does not succeed, you should [contact us](#contact).
Now let's check the traffic between our border router and that of the attachment point:
```shell
ubuntu@ubuntu-xenial:~$ sudo tcpdump -n -i tun0 
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on tun0, link-type RAW (Raw IP), capture size 262144 bytes
13:01:44.103975 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 936
13:01:44.107911 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 936
13:01:44.314913 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:44.718605 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
13:01:45.316115 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:45.714413 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
13:01:46.320350 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:46.785959 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
13:01:47.317784 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 754
13:01:47.321277 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:47.323658 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 754
13:01:47.716653 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
13:01:48.322318 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:48.717883 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
13:01:49.113969 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 933
13:01:49.116672 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 933
13:01:49.322994 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:49.719193 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
13:01:50.324291 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:50.719704 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
13:01:51.325441 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:51.716545 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
13:01:52.326759 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:52.720497 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
13:01:53.327880 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:53.354712 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 755
13:01:53.360066 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 755
13:01:53.722121 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
13:01:54.120803 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 936
13:01:54.123777 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 936
13:01:54.328698 IP 10.0.8.8.50000 > 10.0.8.1.50015: UDP, length 81
13:01:54.721604 IP 10.0.8.1.50015 > 10.0.8.8.50000: UDP, length 81
^C
32 packets captured
32 packets received by filter
0 packets dropped by kernel
```
Let's give a quick explanation: we ran `tcpdump` to print a line every time there is a packet incoming or outgoing in our interface `tun0`. What we see is just the summary of the packets traversing `tun0` back and forth. We are interested in packets between the attachment point and our VM, but anyway we should see traffic referring to only these two IP addresses. In our examples they always were `10.0.8.8` for our VM and `10.0.8.1` for the AP.

We need to see essentially two types of packets right now: small ones with `length 81`, and medium ones that have a variable length. The small ones are sent every 1 second from the AP to our VM, and also from our VM to the AP. If you don't see one of these packets per second, per direction, there is a problem. [Contact us](#contact) in this case. The other, medium size packets, are also being sent from the AP to our VM (they are PCBs), and from our VM to the AP (path registrations). The frequency varies, but you should see them, in both directions, at least once every some 5-10 minutes. If not, please [contact us](#contact).

If you reached this point and still cannot make your `showpaths` and `scmp echo` checks pass from the [Check Request Step](#check-request-paths), please [contact us](#contact).

# Contact
Being this a troubleshooting guide, it is possible that you need to contact us to request further support. The easiest and fastest way to get support is through our [community Google Group](https://groups.google.com/forum/#!forum/scion-community). If you want to contact us for other reasons, please choose the appropriate one from this list:

* For questions, further support and general comments on SCION-related topics, visit our [SCION community Google group](https://groups.google.com/forum/#!forum/scion-community)
* For bug reports, please post them on the [scion-coord github site](https://github.com/netsec-ethz/scion-coord)
* For suggestion on these pages, please post them on the [scion-tutorials GitHub site](https://github.com/netsec-ethz/scion-tutorials)
