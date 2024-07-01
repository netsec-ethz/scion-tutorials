---
title: Home
nav_order: 1
layout: default
---

# Welcome to SCION Tutorials
{: .no_toc }

This website provides step-by-step instructions on how to run a SCION autonomous system (AS) in [SCIONLab](https://www.scionlab.org).
It also provides a list of interesting projects that are using the SCION infrastructure for communication.

**Note**: SCIONLab is a *testbed* network for SCION with *limited performance* and primarily *intended for developers*. If you want to 
connect to the normal public SCION network, please refer to the [official website](https://scion.org) or the [developer documentation](https://docs.scion.org). 


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Quick Start

The following steps describe the quickest path to success to run a SCIONLab AS.
For more detailed information, follow the instructions in the [Installation](content/install/) and
[Configuration](content/config/create_as.html) sections.

1. Register on the [SCIONLab coordination website](https://www.scionlab.org)
2. Navigate to `My ASes` and click `Create a new SCIONLab AS`:
    * Select any of the available attachment points; pick the closest one for shorter latency
    * Enable `Use VPN` and select the installation type `Run SCION in a Vagrant virtual machine`
    * Confirm by clicking `Create AS`
3. Install [Vagrant and VirtualBox](content/install/vm.html)
4. Download the generated tarfile, extract the `Vagrantfile` and start the VM by executing `vagrant up`.



## Introduction

### A brief overview of SCION
SCION (**S**calability, **C**ontrol, and **I**solation **O**n Next-Generation **N**etworks) is an inter-domain network architecture, designed to provide route control, failure isolation, and explicit trust information for end-to-end communication.

SCION organizes ASes into groups of independent routing planes, called isolation domains (ISDs), which interconnect to provide global connectivity.

Its *path-aware* architecture allows end hosts to learn about available network path segments, and combine them into end-to-end paths that are carried in packet headers. Furthermore, thanks to embedded cryptographic mechanisms, path construction is constrained to the route policies of ISPs and receivers, offering path choice to all the parties: senders, receivers, and ISPs.

These features also enable **multi-path communication**, which is an important approach for:

- **high availability**,
- **rapid failover** in case of network failures,
- **increased** end-to-end **bandwidth**,
- **dynamic traffic optimization**, and
- **DDoS** attack **resilience**

SCION is designed to interoperate with the existing networking infrastructure. Deployment of SCION can utilize existing internal routing and forwarding infrastructure of an AS, and only require installation or upgrade of a few border routers. A SCION-IP-Gateway (SIG) in the local infrastructure allows legacy end hosts and applications to be unaware of SCION.

#### References

* [SCION Architecture website](https://www.scion-architecture.net)
* Summary paper: [The SCION Internet Architecture](https://www.scion-architecture.net/pdf/2017-SCION-CACM.pdf)
* Book: [SCION: A Secure Internet Architecture (Open Access PDF)](https://www.scion-architecture.net/pdf/SCION-book.pdf)
* Implementation: [scionproto/scion on GitHub](https://github.com/scionproto/scion)

### SCIONLab, the SCION testbed
SCIONLab is a global research network to test and experiment with the SCION internet architecture. As a participant of SCIONLab, you will be able to create your own ASes that actively participate in the SCION inter-domain routing.

#### Cool, but what is an AS?
An autonomous system (AS) is a _network_ under the control of a single administrative entity or domain.
In SCION, ASes are connected only in well defined locations and links are defined by a provider/customer or a peering relation.

Each AS is in charge of operating a set of control services that participate in the inter-domain routing control plane and provide essential path informations to the collection of devices connected to it, called end hosts (e.g Smartphones, Laptops and so on).
At the same time, an AS is the the granularity of routing in SCION; very roughly, the path information carried in each packet header describes a sequence of ASes that the packet must traverse to reach its destination.


#### What does it mean to run an AS?
Running an AS means running the various AS control plane services and running border routers that connect the AS to other ASes.

For the sake of simplicity, a SCIONLab AS network typically consists of only a single host, which is running both all control plane services, border routers and end host applications at the same time.

Practically speaking your AS will be running on your own hardware, under your full control, and it is as simple as bringing up a *Vagrant* VM.

#### What is an attachment point (AP)?
As already mentioned, the infrastructure of SCIONLab comprises a network of globally connected ASes, and number of these are configured to act as "Attachment Points", and you can choose some as the uplink for your AS. The link between your AS and the attachment point AS is established as an overlay link over the legacy Internet. You can choose whether to instantiate this connection publicly (through a static public IP) or through a VPN offered by the Attachment Point itself (allowing also devices behind a NAT to act as AS).
Whenever a change is made to the configuration of a SCIONLab AS, the configuration of the attachment point AS is automatically updated.


#### What is the relation of SCIONLab and SCION?
The SCIONLab website serves to simplify and coordinate the setup of experimental SCION ASes.
SCIONLab is not connected to the production SCION network and all the SCIONLab ASes have AS-IDs specifically set aside for experimentation.

In order to simplify the management of ASes and lower the entry-barrier for participation, the design of SCIONLab deliberately has some restrictions that are not present in the production deployment of SCION:

- SCIONLab centralizes management of the control plane public key infrastructure. In the real deployment of SCION, there is no such single point of failure.
- Overlay links over the publicly routed Internet are used both in the infrastructure and between the infrastructure and user-owned ASes. Therefore, the security, availability, and performance properties of SCION are not fully realized.


## Contact

* For questions on running your SCIONLab AS and discussion about SCION-related topics, visit our [SCION community mailing list](https://lists.inf.ethz.ch/mailman/listinfo/scion)
* For suggestions or bugs on these pages, please post them on the [tutorial GitHub repo](https://github.com/netsec-ethz/scion-tutorials)
* For bug reports when running SCION, please post them on the [SCIONLab GitHub repo](https://github.com/netsec-ethz/scionlab)
* SCIONLab NOC is available via email <scionlab-admins@sympa.ethz.ch>
