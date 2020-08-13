---
title: SCIONLab AS connectivity requirements
parent: Frequently Asked Questions
nav_order: 11
---

This page lists the connectivity requirements for running a SCIONLab AS. Any firewalls or other network equipment must be configured to allow these.

# Incoming connectivity requirements

| Protocol       | Port         | Source        | Comment |
| :------------- | :----------: | :-----------: | -----------: |
| ALL            |              | ESTABLISHED   | |
| ICMP, ICMP6    |              | 0.0.0.0/0 | Heartbeats |
| UDP | 50000--50010 | 0.0.0.0/0 | SCION inter-AS connectivity |
| UDP | 30000 - 35000 | machines in the same SCION AS | SCION intra-AS connectivity |
| TCP | 22 | 82.130.64.0/18<br> 129.132.0.0/16<br> 195.176.96.0/19<br> 192.33.64.0/18 | Administrative SSH access for configuration management |
| TCP | 443 | 82.130.64.0/18<br> 129.132.0.0/16<br> 195.176.96.0/19<br> 192.33.64.0/18 | Administrative ILO/MGMT access (for physical machines) |

{% include alert type="note" content="
Inter-AS connectivity is required only with the neighbouring ASes. In order to allow dynamic topology adjustments we recommend firewall opening for 0.0.0.0/0. In most cases, after determining the best neighbours for your AS, we can provide a narrowed-down list of networks.
" %}

{% include alert type="note" content="
As an alternative we can also operate connections over a tunnel, e.g. OpenVPN or Wireguard. However please note this will be done only in a special scenarios, e.g. installing a node in a country with strict network policy regarding connectivity abroad. In that case UDP connectivity can be stricter, but inbound SSH connectivity from networks listed above must work.
" %}

{% include alert type="note" content="
The ICMP connectivity is required for diagnosing the state of the network in case of any issues with the node. In case it is not provided, the node will be considered down as soon as it's not reachable via SSH without further investigations.
" %}

# Outgoing connectivity requirements

| Protocol       | Port         | Destination   | Comment          |
| :------------- | :----------: | :-----------: | ---------------: |
| ALL            |              | ESTABLISHED   |                  |
| ICMP, ICMP6    |              | 0.0.0.0/0     | Heartbeats       |
| UDP            | 50000--50010 | 0.0.0.0/0 | SCION inter-AS connectivity |
| UDP            | 30000--35000 | machines in the same SCION AS | SCION intra-AS connectivity |
| TCP            | 80, 443      | 0.0.0.0/0     | Software updates, monitoring |

Additionally, reliable DNS and NTP services must be accessible (but may be provided by the local network).
