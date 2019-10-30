# Joining the SCIONLab infrastructure

If you are part of an organization and/or you are committed to do research with SCION, and using user ASes is not enough for your plans, then you could join SCIONLab with a dedicated host. We have compiled a short guide to document the requirements.
You can join the SCIONLab network as an infrastructure AS with one or more machines, or you can start as small as dedicating only a simple commodity PC.

!!! Danger "Attention needed"

    This page is supposed to give you a general overview over joining as a part of the infrastructure. In any case, if you are interested in joining, please [contact us directly](../index.md#contact).

## Procedure

- [Get in contact with us](../index.md#contact) telling you want to join the infrastructure.
- Once the node(s) are ready on your side, create a `scionlab` user with full `sudo` rights and access for the SCIONLab team.
- The SCIONLab admins will perform measurements to find the most appropriate neighbors to your AS. We will notify you of the result.
- Once the neighboring ASes have been decided, the administrators will install SCION services and configure monitoring for the node(s).
- Your AS is now connected to the infrastructure of SCIONLab and hosts within your network now have direct access to SCIONLab.

Once the node(s) are part of the SCIONLab infrastructure, their configuration will be centrally managed via Ansible in order to keep the whole infrastructure in the best shape. You will not be required to take any action as long as the machine remains accessible for us.

## Requirements

There are a few requirements for you or your organization to join SCIONLab as an infrastructure node:

- Infrastructure ASes and nodes are required to be active 24 hours a day, 7 days a week. The SCIONLab administrators can typically handle all SCION related problems, but sometimes they will contact you if they cannot perform certain tasks. An example would be to change a drive if it failed, etc.
- The machine should have a minimum of 4 CPUs, 8 GB of RAM and 40 GB of disk space. In most of the cases a VM can suffice.
- OS for the SCION infrastructure node must be Ubuntu 18.04.
- The border router node(s) must have a public static IP. Any other SCION services can run with private static IP.
- Firewall has to be configured according to the connectivity matrix below.

## Connectivity requirements

| Protocol       | Port     | Source     | Comment |
| :------------- | :----------: | :-----------: | -----------: |
| UDP | 50000--50010 | 0.0.0.0/0 | SCION inter-AS connectivity |
| UDP | 30000 - 35000 | machines in the same SCION AS | SCION intra-AS connectivity |
| TCP | 22 | 82.130.64.0/18<br> 129.132.0.0/16<br> 195.176.96.0/19<br> 192.33.87.0/24<br> 192.33.88.0/23<br> 192.33.91.0/24<br> 192.33.92.0/24<br> 192.33.93.0/24<br> 192.33.94.0/23<br> 192.33.96.0/21<br> 192.33.104.0/22<br> 192.33.108.0/23<br> 192.33.110.0/24 | Administrative SSH access for configuration management |

!!! note
    Inter-AS connectivity is required only with the neighbouring ASes. In order to allow dynamic topology adjustments we recommend firewall opening for 0.0.0.0/0. In most cases, after determining the best neighbours for your AS, we can provide a narrowed-down list of networks.

!!! note
    As an alternative we can also operate connections over a tunnel, e.g. OpenVPN or Wireguard. However please note this will be done only in a special scenarios, e.g. installing a node in a country with strict network policy regarding connectivity abroad. In that case UDP connectivity can be stricter, but inbound SSH connectivity from networks listed above must work.

### Recommendations

The following are not requirements, but recommendations:

- The border router should be near (latency-wise) the IP border of your AS or organization.
- Co-locating the nodes in your datacenter is usually a good idea as it reduces network latency.
