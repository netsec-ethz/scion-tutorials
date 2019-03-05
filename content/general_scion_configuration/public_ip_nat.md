
# Connecting to SCIONLab where the network has a static public IP address, but the machine itself is behind a Network Address Translation (NAT) device

## Introduction


If your machine was configured as a VM following the steps described in the [tutorial for a virtual machine with static public IP](../virtual_machine_setup/static_ip.md) you don't need to do anything and you can skip this tutorial altogether.

If your machine is not a VM, it should be set up as described in the [tutorial of the host with a public IP address](../general_scion_configuration/public_ip.md) and the changes described below need to be applied before doing further changes to the topology (like adding interfaces or border routers). Since the machine itself is behind a Network Address Translation (NAT) device, however, some adjustments need to be made.

!!! hint
    Sometimes, providers change the IP address of customers unexpectedly. If the IP address changes, then unfortunately the SCION connection to the border router also fails, and then the connection needs to be torn down and re-established from the SCIONLab.org web site. Another approach is to use the approach using a OpenVPN connection, described in the [OpenVPN connection tutorial](../general_scion_configuration/vpn_setup.md).

## Setup

The first step is to find out the internal IP address of your host, as well as the external IP address outside the NAT.

Several web sites offer a service that displays the external IP address of a host, for instance [whatismyip](http://whatismyip.host/). You will need to provide the displayed IPv4 address on the SCIONLab web site in the second step.

The internal IPv4 address can be found with `ifconfig` and spotting the address of the main network connection.

The second step is to complete Steps 1 and 2 of the installation of the basic system as explained in the earlier [tutorial of the host with a public IP address](../general_scion_configuration/public_ip.md), using the external IP address found in the previous step.

The third step is to install port forwarding, so that the two SCION border routers can communicate. Ideally, you can set up port forwarding on your NAT device for UDP port 50000 to the internal IPv4 address of your system. If you also need to specify the IP address and UDP of the other border router that originates the packets, you can look at the `topology.json` file in your gen folder, for instance at this location: `gen/ISD1/AS1026/br1-1026-1/`, adjusting the ISD and AS numbers to your case. In that file, search for the term `Remote`, which specifies the IP address and UDP port from which the connecting packets originate.

!!! hint
    Some networks have several layers of NATs. In those cases, you will need to set up port forwarding for each NAT layer (specifying at each layer the IPv4 address of the next NAT device).

The fourth step re-maps the IP address of the SCION infrastructure devices to the internal address of your host.

For this, you can execute the following command from your main SCION directory (`cd $SC` to get there), replacing `192.168.1.24` with the internal IPv4 address of your host.
```shell
BIND_IP=192.168.1.24; for f in $(find gen -name topology.json); do
    jq ".BorderRouters[].Interfaces[]|=(.BindOverlay={Addr:\"$BIND_IP\", OverlayPort:.PublicOverlay.OverlayPort})" $f > $f.tmp && mv $f.tmp $f; 
done
```

This completes the installation! You can restart the infrastructure in the following way:

```shell
./scion.sh stop
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```

## Next steps

After running the SCION infrastructure, it is necessary to verify that it is running properly. This is covered in the tutorial [Verifying SCION Installation](../general_scion_configuration/verifying_scion_installation.md).
