# Running SCION in a virtual machine &ndash; static public IP address

!!! warning
    If you *do not* have a static public IP address or you cannot receive traffic on UDP port 50000, you should instead [connect to the SCION network via VPN](dynamic_ip).

Simply follow all the steps in the [tutorial for a vpn-based setup](virtual_machine_setup/dynamic_ip) until *Step One &ndash; download a SCION VM*

There, instead of directly clicking on *Create and Download SCIONLab VM Configuration*, first select *My host has a static public IP address and can receive traffic at port 50000.* and enter your host's public IP address in the input field.

Afterwards, follow all subsequent steps in the [tutorial for a vpn-based setup](virtual_machine_setup/dynamic_ip).

!!! warning "Troubleshooting"
    Make sure that your router properly forwards UDP port 50000 to the machine where the SCION VM is running.
