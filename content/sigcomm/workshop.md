---
title: SIGCOMM Workshop Tasks
parent: SIGCOMM2020 SCION Tutorial
nav_order: 10
---

# SCION Tutorial
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

## First steps with SCION

### Access the SCION tutorial page via SCION
We have created a tutorial page about how to use SCION, in particular with SCIONLab. This is normally accessible at [https://netsec-ethz.github.io/scion-tutorials/]. For this tutorial, we have set up a web server at one of the core ASes, 1-ff00:0:1,[127.0.0.1], and a proxy inside your VM. You can access the tutorial page at [http://localhost:9090], the port `9090` is being forwarded into your VM.

You can consult the tutorial page whenever you need additional help for the following exercises. In particular the page *How to use* may be of interest.

### Explore the SCION system and use SCION apps
#### Using the web application
The easiest way to explore the SCION system and the topology is by using the web application at [http://localhost:8080]. Go to the page *Apps* and select one of the apps (*bwtester*, *camerapp*, *sensorapp*). You should not need to change the source settings but you can select any of the preconfigured core ASes as destination or could enter the information of any of your colleagues' ASes.

You can either directly execute the different apps on the *Execute* tab or explore possible paths to your selected destination on the *Paths* tab. Note that you can explore paths to your colleagues' ASes even if they have not yet set up the selected services.

The web application also allows you to look at the TRC of your ISD as well as your certificate and the SCION services running in your AS

#### From a shell inside your VM
Inside your VM, you can explore the subdirectories of `/home/scion/go/src/github.com/scionproto/scion/`.

+ The directory `gen/` contains configuration files for you AS such as the local topology.
+ The directory `logs/` allows you to access logs of the different SCION services. In particular the DEBUG log of the beacon server `bs*.DEBUG` can be interesting.

You can also run the different SCION apps directly from the shell. For that call either `bwtestclient`, `imagefetcher`, or `sensorfetcher` with the option `-s ISD-AS,[Addr]:Port`. The different services run on all core ASes (with ISD-AS `1-ff00:0:1`, `2-ff00:0:2`, and `3-ff00:0:3`) at the local address `127.0.0.1`. The port numbers are 30100 (`bwtestclient`), 42002 (`imagefetcher`), and 42003 (`sensorfetcher`).
The bandwidth tester allows you to explore potential paths by using the *interactive* option `-i`.

Using the tool `bat` that works similar to `curl` you can also download the tutorial page running at `1-ff00:0:1,[127.0.0.1]:9090`. Consult the respective tutorial page for details.

### Run your own SCION apps
#### Bandwidth tester
Running the bandwidth test server is straight forward. On the SCION tutorials page go to *How to use* -> *Bandwidth tester application* and follow the instructions there.

#### Imageserver and sensorserver
You can also set up the apps *imageserver* and *sensorserver* following the instructions on the tutorials website. As you do not have sensors or a camera connected to your VM, you need to use dummy scripts that print some information that you can pipe into *sensorserver* or periodically create jpeg images for *imageserver*. We have prepared two examples: `copy_image.sh` and `systeminfo.sh` that you can adjust to your taste.

## Partner exercises
Select a partner for the remaining exercises. You can choose any of the following exercises in any order.

### Connect to the SCION apps of your partner
If you or your partner have set up any of the server apps in your AS, you can access your partner's apps in the same way as you have accessed the ones on the core ASes.

### Set up SSH and connect to the machine of your partner
We have a prototype implementation of SSH via SCION that is accessible inside your VM at `vagrant/ssh`. Move this directory to the `scion-apps` directory (`/home/scion/go/src/github.com/netsec-ethz/scion-apps/`. You can install and set up SSSH following the instructions in the file `README.md` (you may temporarily have to connect to another WiFi network in order to install the necessary additional software).

### Set up an additional peering link to your partner's AS
The topology of the SCION network can be modified bilaterally by simply setting up additional connections. To that end, perform the following operations:

+ Open the topology file inside your VM at `$SC/gen/ISD*/AS*/br*/topology.json`.
+ In the `Interfaces` section, copy-paste one of the interfaces
+ Adjust the new interface appropriately:
  - Select a new unused interface ID
  - Select a new unused port number in the range 50001-50331 (this range is forwarded from your host into the VM) for the `PublicOverlay`
  - Set the `LinkTo` to `PEER` (if you and your partner are in the same ISD you can also use `PARENT`/`CHILD`)
  - Set `RemoteOverlay` to the public IP of your partner and their selected port number
  - Set `ISD_AS` to your partner's ISD and AS
  - If you like, you can also modify `Bandwidth` and `MTU`
+ Save the modified topology file
+ Copy the modified topology file into all subdirectories of `$SC/gen/ISD*/AS*/` (all other services like beacon and path server need to know the new topology)
+ Restart SCION by calling `sudo systemctl restart scion.service`; you may also have to remove the directory `$SC/gen-cache`

You can check that the new link is working on the web application at [http://localhost:8080].

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
