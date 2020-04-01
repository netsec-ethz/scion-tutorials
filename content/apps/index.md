---
title: Applications
nav_order: 5
has_children: true
---

# Applications

This section describes sample applications with SCION support. These applications are maintained in our [scion-apps GitHub repository](https://github.com/netsec-ethz/scion-apps).

## Running

All of these applications require a running SCION endhost stack, i.e. a running
SCION dispatcher and SCION daemon. This can be on the machine running your
entire SCIONLab AS or a separate end host. Please refer to setup instructions
the [end host configuration section](../config/setup_endhost.html).

### Hostnames
All applications allow to specify remote hosts either by SCION addresses of the
form `<ISD>-<AS>,[<IP>]`, or as a hostname.
Hostnames are resolved by scanning the `/etc/hosts` file or by a RAINS lookup.

Hosts can be added to `/etc/hosts` by adding lines like this:

```
# The following lines are SCION hosts
17-ffaa:1:10,[10.0.8.100]	server1
18-ffaa:0:11,[10.0.8.120]	server2
```

Please refer to the [section on RAINS](rains.html) on how to configure name resolution using RAINS.

### Multiple local end host stacks

When running multiple local ASes, e.g. during development, the address of the
sciond corresponding to the desired local AS needs to be specified in the
`SCION_DAEMON_ADDRESS` environment variable.

Please refer to the documentation in the [scion-apps/README](https://github.com/netsec-ethz/scion-apps#running).

