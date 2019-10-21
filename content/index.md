# Welcome to SCION Tutorials

## Introduction

This website provides step-by-step instructions on how to run a SCION autonomous system (AS) in [SCIONLab](https://www.scionlab.org).
It also provides a list of interesting projects that are using the SCION infrastructure for communication.

### A brief overview of SCION
SCION (**S**calability, **CO**ntrol and **I**solation on next-generation **N**etworks) is an inter-domain network architecture, designed to provide route control, failure isolation, and explicit trust information for end-to-end communication.

SCION organizes ASes into groups of independent routing planes, called isolation domains (ISDs), which interconnect to provide global connectivity.

Its *path-aware* architecture allows end hosts to learn about available network path segments, and combine them into end-to-end paths that are carried in packet headers. Furthermore, thanks to embedded cryptographic mechanisms, path construction is constrained to the route policies of ISPs and receivers, offering path choice to all the parties: senders, receivers, and ISPs.

These features also enable **multi-path communication**, which is an important approach for:

- **high availability**,
- **rapid failover** in case of network failures, 
- **increased** end-to-end **bandwidth**, 
- **dynamic traffic optimization**, and 
- **DDoS** attacks **resilience**

SCION is designed to interoperate with the existing networking infrastructure. Deployment of SCION can utilize existing internal routing and forwarding infrastructure of an AS, and only require installation or upgrade of a few border routers. A SCION-IP-Gateway (SIG) in the local infrastructure allows legacy end hosts and applications to be unaware of SCION.

Please refer to the [SCION Architecture website](https://www.scion-architecture.net) for more information. 

### SCIONLab, the SCION testbed
SCIONLab is a global research network to test and experiment with the SCION internet architecture. As a participant of SCIONLab, you will be able to create your own ASes that actively participate in the SCION inter-domain routing.

#### Cool, but what is an AS?
A SCION AS is the entity in charge of providing essential informations to a collection of devices connected to it, called end hosts (e.g Smartphones, Laptops and so on).
The ASes in SCION are fundamental in the two main phases of the architecture: the *control* plane, which is the process responsible for discovering paths and making those paths available to end hosts; and the *data* plane, which is the process responsible for the transmission of the packets.

In the former case, the ASes are the entities which actually perform the process.
In the latter, whenever a non intra-AS communication happens (in which existing communication protocols suck as IP can be leveraged), the ASes are in charge of correctly delivering the packets to the end host.

#### What does it mean to run AS?
Practically speaking your AS will be running on your own hardware, under your full control, and it is as simple as bringing up a *Vagrant* VM.

#### How does SCIONLab work
The SCIONLab website serves to simplify and coordinate the setup of experimental ASes.
In order to simplify the management of ASes and lower the entry-barrier for participation, the design of SCIONLab deliberately has some restrictions that are not present in the production deployment of SCION:

- SCIONLab centralizes management of the control plane public key infrastructure. In the real deployment of SCION, there is no such single point of failure.
- Overlay links over the publicly routed Internet are used both in the infrastructure and between the infrastructure and user-owned ASes. Therefore, the security, availability, and performance properties of SCION are not fully realized.

##### Configure your AS
SCIONLab offers a WebApp that lets you configure your AS quickly and with ease. <br/>
All you need to do is the following: 

- choose the type of installation that you prefer (e.g. *Vagrant* VM, from packages or custom), 
- specify your connection preferences, such as whether or not to use a VPN, or which ports to assign to SCION,
- and which attachment points (APs) you want to attach to

After the AS has been created you will be able to download the bundled configuration files for the SCION services and run your AS.


##### What is an attachment point (AP)?
As already mentioned, the infrastructure of SCIONLab comprises a network of globally connected ASes, and number of these are configured to act as "Attachment Points", and you can choose some as the uplink for your AS. The link between your AS and the attachment point AS is established as an overlay link over the legacy Internet. Again, you can choose whether to instantiate this connection publicly (through a static public IP) or through a VPN offered by the Attachment Point itself (allowing also devices behind a NAT to act as AS).

!!! TODO
    Proper introduction:

    - > what is SCION
    - > what is SCIONLab, how is it related to SCION
    - > what is an AS, what does it mean to run an AS
    - > how does SCIONLab work, what is an AP, how is the connection set up
    - overlay over public internet, VPN
    - role of the coordinator
    - references (READMEs, wiki, papers, ..)

Once your AS is running and connected to the SCIONLab infrastructure, you can explore applications using SCION.


## Quick Start

The following steps describe the quickest path to success to run a SCIONLab AS.
For more detailed information, follow the instructions in the [Installation](install/index.md) and 
[Configuration](config/create_as.md) sections.

1. Register on the [SCIONLab coordination website](https://www.scionlab.org)
2. Navigate to `My ASes` and click `Create a new SCIONLab AS`:
    * Select any of the available attachment points; pick the closest one for shorter latency
    * Enable `Use VPN` and select the installation type `Run SCION in a Vagrant virtual machine`
    * Confirm by clicking `Create AS`
3. Install [Vagrant and VirtualBox](install/vm.md)
4. Download the generated tarfile, extract the `Vagrantfile` and start the VM by executing `vagrant up`.

[//]: # (TODO: Screenshots)



## Contact

* For questions on running your SCIONLab AS and general discussion about SCION-related topics, visit our [SCION community mailing list](https://lists.inf.ethz.ch/mailman/listinfo/scion)
* For bug reports, please post them on the [scionlab GitHub site](https://github.com/netsec-ethz/scionlab)
* For suggestion on these pages, please post them on the [scion-tutorials GitHub site](https://github.com/netsec-ethz/scion-tutorials)

!!! TODO
    Bugs for the different repos; scionproto/scion, netsec-scion, scion-apps, rains, ...?
    
    Should we invite people to ask on slack?
