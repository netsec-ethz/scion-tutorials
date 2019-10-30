# Troubleshooting


## VPN

The following relate to AS [configured to use VPN](../config/create_as.md#configure-a-scionlab-as).


#### VPN connection fails

Check the OpenVPN client log for specific information, using `sudo journalctl -e -u openvpn`.

An unspecific timeout typically indicates that your behind a firewall that blocks UDP port 1194. Please contact your network administrators to unblock this port.


#### Border Router fails to start

If you see (e.g. that your border router did not start, it is likely that the border router was trying to start before the VPN tunnel interface was up.

Simply try again;

    sudo systemctl start openvpn@client

    # ... wait a bit ... maybe check the status of the openvpn client
    sudo systemctl status openvpn@client
    ip address show dev tun0

    sudo systemctl restart scionlab.target



## SCION Services

The following are common issues or troubleshooting strategies for a SCION AS.

#### Service fails to start

Typically, this indicates a configuration error.

If you've configured to use VPN, check the "Border Router fails to start" entry in the VPN section above.

Inspect the logs of the failed services to find details.
In case of multiple failures, fixing issues in the following order usually works best:
* dispatcher
* sciond
* border routers
* anything else


!!! Tip
    First clear the `/var/log/scion/` directory and restart. This often helps to find the relevant log messages quicker.


#### Not receiving beacons

Log of the beacon server (`/var/logs/scion/bs*.log`) does not contain recent entries referring to `Registered beacons`.

As at the time of writing there are certain failure modes of the border routers that are hard to diagnose and are fixed with a simple restart, first thing to try is to turn it off and on again:

```
sudo systemctl restart scionlab.target
```

## Getting help

If your stuck, don't hesitate to [get in contact](../index.md#contact)
