---
title: Joining the SCIONLab infrastructure
parent: Frequently Asked Questions
nav_order: 10
---

# Joining the SCIONLab infrastructure

If you are part of an organization and/or you are committed to do research with SCION, and using user ASes is not enough for your plans, then you could join SCIONLab with a dedicated host. We have compiled a short guide to document the requirements.
You can join the SCIONLab network as an infrastructure AS with one or more machines, or you can start as small as dedicating only a simple commodity PC.

{% include alert type="danger" title="Attention needed" content="
This page is supposed to give you a general overview over joining as a part of the infrastructure. In any case, if you are interested in joining, please [contact us directly](../../#contact).
" %}

## Procedure

1. [Get in contact with us](../../#contact) telling you want to join the infrastructure.
2. Once the node(s) are ready on your side, create a `scionlab` user with full `sudo` rights and access for the SCIONLab team.
3. The SCIONLab admins will perform measurements to find the most appropriate neighbors to your AS. We will notify you of the result.
4. Once the neighboring ASes have been decided, the administrators will install SCION services and configure monitoring for the node(s).
5. Your AS is now connected to the infrastructure of SCIONLab and hosts within your network now have direct access to SCIONLab.

Once the node(s) are part of the SCIONLab infrastructure, their configuration will be centrally managed via Ansible in order to keep the whole infrastructure in the best shape. You will not be required to take any action as long as the machine remains accessible to us.

## Requirements

There are a few requirements for you or your organization to join SCIONLab as an infrastructure node:

- Infrastructure ASes and nodes are required to be active 24 hours a day, 7 days a week. The SCIONLab administrators can typically handle all SCION related problems, but sometimes they will contact you if they cannot perform certain tasks. An example would be to change a drive if it failed, etc.
- The machine should have a minimum of 4 CPUs, 8 GB of RAM and 40 GB of disk space. In most of the cases a VM can suffice.
- OS for the SCION infrastructure node must be Ubuntu 18.04.
- The border router node(s) must have a public static IP address. Any other SCION services can run with private static IP addresses.
- Any firewalls affecting the node must be configured according to the [SCION AS connectivity matrix](./as_connectivity.html).

## Recommendations

The following are not requirements, but recommendations:

- The border router should be near (latency-wise) the IP border of your AS or organization.
- Co-locating the nodes in your datacenter is usually a good idea as it reduces network latency.
- Given the fact SCIONLab is a research network dedicated mainly for running experiments, you may want to place the SCIONLab node(s) in a DMZ or a dedicated subnet.
