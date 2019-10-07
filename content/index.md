# Welcome to SCION Tutorials

## Introduction

This website provides step-by-step instructions on how to run a SCION autonomous system (AS) in [SCIONLab](https://www.scionlab.org).
It also provides a list of interesting projects that are using the SCION infrastructure for communication.

!!! TODO
    Proper introduction:

    - what is SCION
    - what is SCIONLab, how is it related to SCION
    - what is an AS, what does it mean to run an AS
    - how does SCIONLab work, what is an AP, how is the connection set up
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
