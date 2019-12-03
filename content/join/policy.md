---
title: Policy
parent: Joining the infrastructure
nav_order: 10
---

# Policy

In order to ensure proper testbed operation and maintenance we require that all sites (external and internal) abide by the policies outlined below.

1. Provide one or more dedicated machines on-site to act as the SCIONLab nodes. The machine should not be used for any other tasks beyond its role as a SCIONLab node.
2. Provide root (or sudo) access to the SCIONLab management institution (currently ETH Zurich).
3. Allow the SCIONLab management institution to install, remove and update SCIONLab-related software as needed for the operation and maintenance of the network.
4. Work with the SCIONLab management institution to solve any firewall and tunneling issues that may arise during the installation, configuration and operation of the SCIONLab node(s).
5. Provide the name, email and telephone information of an easily and quickly reachable site-specific operator who will be the main contact whenever issues arise with the local SCIONLab node(s). A backup operator, while not required, is highly encouraged. Operator email addresses must belong to the institution. All local operators will be required to join and regularly monitor the SCIONLab community mailing list and are required to respond to requests from the testbed management institution in a reasonable time, but no more than 3 working days.
6. Ensure that the machine has limited logins beyond the SCIONLab account. A local operator account is highly encouraged, but general user accounts should not be present on the SCIONLab node. In addition, only SCIONLab-related software should be allowed to run on the machine.
7. The machine may be a dedicated physical box or a virtual machine with a publicly routable IP address (i.e., should not be behind a NAT). The local operators should ensure that if the machine has usage limits or other restrictions (for example VMs on cloud services), those will not interfere with proper testbed operation.
8. In case more than one machine is being provided by the institution, it is allowed if only one of them has a publicly routable IP address whereas the others use private static IP.
9. All SCIONLab nodes will run the same operating system, i.e. Ubuntu LTS release. Updates to the operating system will be managed by the SCIONLab network operations team. The local operator at a site will be responsible for the initial operating system installation. Specific instructions for that installation will be made available.
10. Suggested Hardware: 4 CPUs, 8 GB of RAM and 40 GB of disk space.
11. Suggested Location: SCIONLab node should reside in a machine room in a rack.
12. Suggested Network Connectivity: There is no minimum bandwidth requirement for SCIONLab nodes; however, higher network speeds are both recommended and desirable.
13. Participating institutions may disconnect from the testbed upon giving appropriate (at least 24 hours) notice to the SCIONLab testbed management institution. The same policy applies if the participating institution wishes to turn off the machine for maintenance and/or hardware upgrades. Early notification will allow the testbed management institution to take any actions necessary to prevent unnecessary disruption of service to the testbed.
14. We strongly discourage and will not accept requests to join the SCIONLab testbed if there is no intention to use it for productive research. Connecting to the testbed for the sake of being connected is a violation of the current policies.
15. The SCIONLab testbed management institution reserves the right to refuse or disconnect from the testbed any node that is found not to comply with the above policies, engages in abusive behavior, has non-responsive operators, and in general is deemed to either add no value or be a detriment to the general testbed operation.

If the above policies cannot be met, we still encourage new institutions to [contact the SCIONLab team](../../#contact) to discuss connection with the testbed. Connection approval lies solely with the SCIONLab testbed management institution, but every effort will be made to accommodate connection requests.

{% include alert type="note" content="
The policy is based on the [Policies for Connecting Nodes to the NDN Testbed](https://named-data.net/ndn-testbed/policies-connecting-nodes-ndn-testbed/)
" %}
