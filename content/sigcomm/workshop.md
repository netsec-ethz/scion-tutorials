---
title: SIGCOMM Workshop Tasks
parent: SIGCOMM2020 SCION Tutorial
nav_order: 10
---


# SCION Tutorial
<!--
## Preparation
+ You have received a USB stick according to your IP address; check that the IP address written on the stick matches the one of your computer
+ Install the required software if you haven't done so already (you may need to switch to the standard WiFi as the *SCIONLab* network has no Internet connection)
  - Install [vagrant](https://www.vagrantup.com/downloads.html)
  - Install [virtualbox](https://www.virtualbox.org/wiki/Downloads)
+ Copy the contents of this USB stick to an arbitrary directory DIR
+ Open a terminal and move to DIR
+ Type `vagrant box add paduabox padua.box`; this imports the virtual machine image
+ Type `vagrant up`; this boots and provisions the virtual machine; if this takes more than 2-3 minutes, let us know
+ Type `vagrant ssh`; if prompted for a password type `scion`
+ You should now be connected to the virtual machine
### Exchange files between host and VM
The directory where you copied the `Vagrantfile` and started the VM is mounted inside the VM at the location `/vagrant`. You can use these directories if you need to copy files from the host to the VM or vice-versa.
## Check that everything works
+ On your host system, open [http://localhost:8080] in your browser
  - This should show the **SCIONLab Health Check**
  - Don't worry about the *Border router ICMP test* and *Border router SCMP test*; some other nodes may not be running yet
  - All other tests should be passed; if not, let us know
  - *Additional information*: The port `8080` is being forwarded into the VM where the website is served. With this, you can access and explore the SCION system inside the VM.
+ You can also check the system from your VM:
  - Go to the SCION directory by typing `cd $SC`
  - Look at the debug log of the *beacon server* by typing, e.g.,  `tail -f logs/bs*.DEBUG`
  - You should see messages like `Successfully verified PCB ...`, `Propagated x PCBs to ...`, and `Registered x up-segments`
+ If you experience any problems, let us know and we will help you
-->

## First steps with SCION
### Starting the VM
Open a terminal in your host OS and change the working directory to were the `Vagrantfile` from SCIONLab is. You have to start up the VM, and for that you can execute:
```shell
vagrant up
```
Some messages will be displayed, indicating the progress of the VM creation. The command should exit with zero (you can check with `echo $?` right after `vagrant up` has finished).

Everytime you want to log in to your VM you can go to the directory where the `Vagrantfile` is, and execute:
```shell
vagrant ssh
```
That will open a shell terminal inside your VM.

At any point you can check if the services are running with `sudo systemctl list-dependencies scionlab.target`. It should list at least 4 services: border router, control service, daemon and dispatcher must be running.

<a name="scmp_echo"></a>
Now that you have a terminal open in your VM, check connectivity to the infrastructure. We can perform an SCMP echo, which is similar to an ICMP echo but with SCION, by running:
```shell
scmp echo -remote 18-ffaa:0:1201,128.237.152.180
```
This sends SCMP echo packets to a computer with IP `128.237.152.180` in AS `18-ffaa:0:1201`. This computer is part of the SCIONLab infrastructure, and should reply to those echo packets.
The command should output the reply packets arriving shortly after, with some milliseconds latency. Of course, you can send SCMP echo packets to other computers in other ASes. Try with those of your colleagues.

<!--
### Access the SCION tutorial page via SCION
We have created a tutorial page about how to use SCION, in particular with SCIONLab. This is normally accessible at [https://netsec-ethz.github.io/scion-tutorials/]. For this tutorial, we have set up a web server at one of the core ASes, 1-ff00:0:1,[127.0.0.1], and a proxy inside your VM. You can access the tutorial page at [http://localhost:9090], the port `9090` is being forwarded into your VM.

You can consult the tutorial page whenever you need additional help for the following exercises. In particular the page *How to use* may be of interest.
-->

### Explore the SCION system and use SCION apps
#### Using the web application
The easiest way to explore the SCION system and the topology is by using the SCIONLab User AS web application. We are going to install it by login into your VM (`vagrant ssh` as mentioned before), and then running:
```shell
sudo apt-get install -y scion-apps-webapp
sudo systemctl start scion-webapp.service
```
With that, the web application should be running inside the VM, listening for connections on port `8000`. Because the VM is configured to forward that port, you can already connect from your host machine at [http://localhost:8000]. In this page, the health status of your user AS is displayed. As mentioned in the [preparation guide](preparation.html), all the sections in the health check should be passing, and displayed in green.

Go to the page *Apps* and select one of the apps (*bwtester*, *camerapp*, *sensorapp*). You should not need to change the source settings but you can select any of the ASes preconfigured in the list as destination or could enter the information of any of your colleagues' ASes.

You can either directly execute the different apps on the *Execute* tab or explore possible paths to your selected destination on the *Paths* tab. Note that you can explore paths to your colleagues' ASes even if they have not yet set up the selected services.

The web application also allows you to look at the TRC of your ISD as well as your certificate and the SCION services running in your AS

#### From a shell inside your VM
Inside your VM, you can explore several subdirectories:

+ The directory `/etc/scion/gen/` contains configuration files for you AS such as the local topology.
+ The directory `/var/log/scion/` allows you to access logs of the different SCION services. In particular the control service's log `cs*-1.log` can be interesting.

You can also run the different SCION apps directly from the shell. For that call either `scion-bwtestclient`, `scion-imagefetcher`, or `scion-sensorfetcher` with the option `-s ISD-AS,[Addr]:Port`. 
The bandwidth tester allows you to explore potential paths by using the *interactive* option `-i`.

There are already several servers just for testing:

+ sensorserver at `17-ffaa:0:1102,[192.33.93.177]:42003`
+ cameraserver at `17-ffaa:0:1102,[192.33.93.166]:42002`
+ bwtestserver at [list of bwtestservers](http://localhost:4000/content/apps/bwtester.html#scionlab-bandwidth-test-servers)

Be sure to visit the general tutorial about some existing SCION applications [located here](../apps/index.html)

<!-- Using the tool `bat` that works similar to `curl` you can also download the tutorial page running at `1-ff00:0:1,[127.0.0.1]:9090`. Consult the respective tutorial page for details. -->

### Run your own SCION apps
#### Bandwidth tester
Running the bandwidth test server is straight forward. On the SCION tutorials page go to *How to use* -> *Bandwidth tester application* and follow the instructions there.

#### Imageserver and sensorserver
You can also set up the apps *imageserver* and *sensorserver* following the instructions on the tutorials website. As you do not have sensors or a camera connected to your VM, you need to use dummy scripts that print some information that you can pipe into *sensorserver* or periodically create jpeg images for *imageserver*. 
<!-- We have prepared two examples: `copy_image.sh` and `systeminfo.sh` that you can adjust to your taste. -->

### Set up an additional peering link with your second user AS
#### Configure a second user AS
Configure another user AS in [SCIONLab](https://www.scionlab.org). Download the tar file, uncompress it like you did with your first AS.
Now, since we are going to run this VM at the same time as we also run the first one, you have to edit the `Vagrantfile` and comment out with a `#` the following line:
```ruby
config.vm.network "forwarded_port", guest: 8000, host: 8000, protocol: "tcp"
```
Add a new line right before `config.vm.provider "virtualbox" do |vb|` in the `Vagrantfile`. It will configure a new interface in the VM with that IP:
```ruby
config.vm.network :private_network, ip: "10.0.0.20"
```

You might also want to edit the settings about the memory reserved to the VM with the line `vb.memory = "2048"`. Setting it to `1024` should also work okay, if your host machine starts to run out of memory.

After saving the file, proceed like with the first VM:
+ `vagrant up`
+ `vagrant ssh`
+ Check connectivity to the infrastructure [with scmp echo](#scmp_echo).
+ Output services running with `sudo systemctl list-dependencies scionlab.target`

#### Reconfigure your first user AS
Now go back to the directory where you have the `Vagrantfile` for your first VM and stop it by running:
```shell
vagrant halt
```
Edit the `Vagrantfile` to add a new interface, like you did with your second AS, but with a different IP. So, right before the line `config.vm.provider "virtualbox" do |vb|` add the following:
```ruby
config.vm.network :private_network, ip: "10.0.0.10"
```
Save the file and run the VM again with `vagrant up`. Once it has booted, check again connectivity with [scmp echo](#scmp_echo).
If everything is still fine, you can ping the other machine with a normal IP ping command:
```shell
ping 10.0.0.20
```
This will send ICMP packets to the second VM, and it should reply normally.

Right now, the two VMs have a private network where they can communicate directly, using `10.0.0.10` and `10.0.0.20` as their IPs.

#### Edit the topology files
Go to your first VM with `vagrant ssh`.
Inside `/etc/scion/gen` there will be an `ISD*` directory, with an `AS*` directory. Change directory to the `sciond` service by running:
```shell
cd /etc/scion/gen/ISD*/AS*/endhost
```
Now when you list the files, there will be one called `topology.json`. Open it with your favorite editor, e.g. `vim`:
```shell
sudo cp topology.json topology.json.bak  # create a backup copy of the file, convenient
sudo vim topology.json
```
+ In the `Interfaces` section, copy-paste one of the interfaces
+ Adjust the new interface appropriately:
  - Select a new unused interface ID e.g. `2`.
  - Set the `LinkTo` to `PEER`
  - Set the `PublicOverlay` to `10.0.0.10`, port `50001`
  - Set the `RemoteOverlay` to `10.0.0.20`, port `50001`
  - Set `ISD_AS` to your second user AS ISD and AS ID.
+ Save the modified topology file
+ Copy the modified topology file into all subdirectories of `/etc/scion/gen/ISD*/AS*/` (all other services need to know the new topology)
+ Restart SCION by calling `sudo systemctl restart scionlab.target`; you may also have to remove the contents of the directory `/etc/scion/gen-cache/`
The added section to the topology file should look like this (where `ISD2-ffaa:1:YYYY` is the IA of your second user AS);
```json
  "2": {
    "Bandwidth": 1000,
    "ISD_AS": "ISD2-ffaa:1:YYYY",
    "LinkTo": "PEER",
    "MTU": 1472,
    "Overlay": "UDP/IPv4",
    "PublicOverlay": {
      "Addr": "10.0.0.10",
      "OverlayPort": 50001
    },
    "RemoteOverlay": {
      "Addr": "10.0.0.20",
      "OverlayPort": 50001
    }
  }
```

As always, restart services with `sudo systemctl restart scionlab.target` and check connectivity to the infrastructure with [scmp echo](#scmp_echo).

When it's clear that the first AS works fine, adapt the topology files of the second AS. Remember to write the correct IA of the first AS, and to use the correct IPs for the public and remote overlays.
The section should look like this (where `ISD1-ffaa:1:XXXX` is the IA of your first AS):
```json
  "2": {
    "Bandwidth": 1000,
    "ISD_AS": "ISD1-ffaa:1:XXXX",
    "LinkTo": "PEER",
    "MTU": 1472,
    "Overlay": "UDP/IPv4",
    "PublicOverlay": {
      "Addr": "10.0.0.20",
      "OverlayPort": 50001
    },
    "RemoteOverlay": {
      "Addr": "10.0.0.10",
      "OverlayPort": 50001
    }
  }
```
Like before, copy the `topology.json` file to all subdirectories of `/etc/scion/gen/ISD*/AS*/`. Then restart services with `sudo systemctl restart scionlab.target`.

#### Check peering link
After waiting some seconds for the beacons to be propagated, you should now be able to use this new path in any application. Let's first find out if the peering path is visible by running:
```shell
showpaths -dstIA ISD2-ffaa:1:YYYY
```
This will print many lines with some of the possible paths from the two ASes. As an example, one of the first lines that you should see would look like:
```
[ 0] Hops: [19-ffaa:1:XXXX 2>2 17-ffaa:1:YYYY] MTU: 1472, NextHop: 127.0.0.1:31045
```
When run from `19-ffaa:1:XXXX` looking for `17-ffaa:1:YYYY`.

Many of the applications accept a `-i` switch (interactive) that allows you to select the path manually. Use it with `scmp echo` and observe that the latency is much lower than going upstream and down again.

## Partner exercises
Select a partner for the remaining exercises. You can choose any of the following exercises in any order.

### Connect to the SCION apps of your partner
If you or your partner have set up any of the server apps in your AS, you can access your partner's apps in the same way as you have accessed the ones on the core ASes.

<!--
### Set up SSH and connect to the machine of your partner
We have a prototype implementation of SSH via SCION that is accessible inside your VM at `vagrant/ssh`. Move this directory to the `scion-apps` directory (`/home/scion/go/src/github.com/netsec-ethz/scion-apps/`. You can install and set up SSSH following the instructions in the file `README.md` (you may temporarily have to connect to another WiFi network in order to install the necessary additional software).
-->


### Set up an additional peering link to your partner's AS
Similarly to the peering exercise above, you can configure a new peering interface to your partner. You will need to know their IA, their public IP and port. For this both you and your partner must have public IP addresses (or the ability to "punch a hole" in the firewall of your network or NAT setup).

+ Open the topology file and add a new interface with the correct IA, public IP and port.
+ Save the modified topology file and copy it into all subdirectories of `/etc/scion/gen/ISD*/AS*/`
+ Restart SCION by calling `sudo systemctl restart scionlab.target``


### Netcat
At any point you can install the scion netcat application with:
```shell
sudo apt-get install scion-apps-netcat
```
The application supports a subset of the regular BSD netcat command. Run netcat in listening mode in a machine of one AS like:
```shell
scion-netcat -u -l 40002
```
And netcat connecting with:
```shell
scion-netcat -u ISD1-ffaa:1:XXXX,[127.0.0.1]:40002
```
Where `ISD1-ffaa:1:XXXX` is the IA of your other user AS. You can type in any of the running processes, and it will echo it to the other side.


<!--
### Perform latency measurements with your partner
#### Use existing timestamp-based application
We have provided a simple implementation for timestamp-based latency measurements that you can access at `vagrant/latency`. Move this directory into your `$GOPATH`, e.g., to `/home/scion/go/src/local`. Inside that directory, compile the applications using `go build timestamp_server.go` and `go build timestamp_client.go`. You can now run the server and client on different machines and perform latency estimations.

#### Create your own latency estimation
This implementation uses timestamps that allow for uni-directional measurements but require synchronized clocks. Use the existing implementation as a starting point for your own implementation that does not use timestamps but instead does round-trip-time (RTT) measurements.

### Create your own SCION app (based on existing apps)
Now you can be creative: Use any of the existing SCION applications as an inspiration to create your own SCION application. 
The first persons that manage to create a working SCION application (that is sufficiently different from existing ones, for some definition of *sufficient*) will win a prize.

## Using SCION on your own
If you want to continue experimenting with SCION after this tutorial, there are two options:

+ Use SCION with SCIONLab:
  - Sign up at [https://www.scionlab.org]
  - Create a SCIONLab AS
  - Download the VM and set it up similar to the VM in this tutorial
+ Use SCION with a local topology
  - You can reuse the existing VM
  - Remove the `$SC/gen` and `$SC/gen-cache` directory
  - Inside `$SC` run `./scion.sh topology nodocker -c configfile` in order to create a local topology; you can specify a topology configuration file from the `topology/` subdirectory
  - After calling `sudo systemctl restart scion.service`, SCION will run with your new local topology
  - Note that in many SCION apps you may need to use the additional option `-sciondFromIA`
  -->
