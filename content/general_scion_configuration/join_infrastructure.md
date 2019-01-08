# Join the Infrastructure

If you are part of an organization and/or you are commited to do research with SCION, and using user ASes is not enough for your plans, then you could join SCIONLab with a dedicated host. We have compiled a short guide to document the requirements.
You can join the SCIONLab network as an infrastructure AS with one or more machines, or you can start as small as dedicating only a simple commodity PC.
If you have decided to join SCIONLab as a permanent node in the infrastructure, check that your new node(s) can comply with the requirements. Then, just follow the procedure!

## Requirements

There are a few requirements for you or your organization to join SCIONLab as an infrastructure node:

- Infrastructure ASes and nodes are required to be active 24 hours a day, 7 days a week. The SCIONLab administrators can typically handle all SCION related problems, but sometimes they will contact you if they cannot perform certain tasks. An example would be to change a drive if it failed, etc.
- The machine must have a minimum of 4 Gb of RAM. A VM can suffice, given sufficient resources.
- Currently the SCION code works with Ubuntu 16.04.
- The border router node(s) must have a public static IP.
- The following are the traffic types and the corresponding ports SCIONLab requires at this moment.
    - SSH: TCP 22: source 192.33.96.0/20, 192.33.88.0/21, 192.33.87.0/24, 54.176.0.0/12
    - OpenVPN: UDP 1194: source any
    - HTTP: TCP 8000: source any
    - Monitoring: TCP 8080,9200,9900: source any
    - Management: TCP 9100: source any
    - Scion packet: UDP 50000~50010: source any

### Recommendations

The following are not requirements, but recommendations:

- The border router should be near (latency wise) the IP border of your AS or organization.
- Co-locating the node or nodes in your datacenter is usually a good idea in terms of network latency.
- To join the SCION network, we have a specific hardware recommendation: [HP Proliant DL20 gen9](https://www.hpe.com/us/en/product-catalog/servers/proliant-servers/pip.specifications.hpe-proliant-dl20-gen9-server.1008556817.html).
We further customize the machine with additional 8G ECC Ram and SSD, but a regular HDD also works.
- Instead of a blade type server machine, a regular PC with a similar spec works as well.



## Procedure

You can connect to the SCIONLab infrastructgure through the following steps:

- Get in contact with us. Send us an email to <scionlab-admins@sympa.ethz.ch> telling us you want to join the infrastructure.
- Once the node(s) are ready, create a user with the name `scion` and permission to run `sudo`. Grant the SCIONLab admins `ssh` access to the machine via a key for that `scion` user.
- The SCIONLab admins will perform some measurements to find the appropriate neighbors to your AS. We will notify you of the result.
- Once the neighboring ASes have been decided, the administrators will install the necessary services of SCION and monitoring. This is typically done by us using `Ansible`. We deploy the configuration of the node(s) in the AS at the same time.
- Your AS is now connected to the infrastructure of SCIONLab and hosts within your network now have direct access to SCIONLab.
