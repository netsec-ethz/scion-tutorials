---
title: RHINE Name Resolution Service
parent: Applications
nav_order: 70
---

# Test RHINE servers in SCIONLAB

There are now two authoritative [scion-coredns](https://github.com/netsys-lab/scion-coredns/tree/rhine) RHINE test servers running in SCIONLab under `19-ffaa:1:fe4,127.0.0.1` (port 53) and under `17-ffaa:1:1008,127.0.0.1` (also port 53) respectively.

## Resolver config

To make use of these name servers from within a SCIONLab user AS,
[scion-sdns](https://github.com/netsys-lab/scion-sdns) can be
configured as a recursive resolver for that AS.

The `config.yml` file for `scion-sdns` needs at least the following:

```yaml
# Address to bind to for the DNS server
bind = "0.0.0.0:53"

# Enable SCION
scion = true

# RHINE certificate to validate RRs with
cacertificatefile = "/path/to/CACert.pem"

# Root zone SCION servers
rootscionservers = [
"17-ffaa:1:1008,127.0.0.1:53",
"19-ffaa:1:fe4,127.0.0.1:53"
]

# What kind of information should be logged, Log verbosity level [crit,error,warn,info,debug]
loglevel = "debug"

# List of locations to recursively read blocklists from (warning, every file found is assumed to be a hosts-file or domain list)
blocklistdir = "bl"

# Which clients allowed to make queries
accesslist = [
"0.0.0.0/0",
"::0/0"
]
```

The `certificatefile` field should point to a local copy of the
[current SCIONLab RHINE root certificate](https://github.com/netsys-lab/scion-rains/blob/master/testdata/scionlab/CACert.pem)
(valid until August 2023).

Note: For `scion-sdns` to be able to listen on port 53, it might be necessary to `systemctl stop
[systemd-resolved.service](https://systemd.network/systemd-resolved.service.html)`. This
may in turn impact regular DNS name resolution on the host.

Change the `nameserver` entry in `/etc/resolv.conf` to the IP address
of the `scion-sdns` recursive resolver. A typical SCIONLab user AS
runs on just a single host, on which the resolver should be deployed
as well. In that case, `/etc/resolv.conf` should simply point to `localhost`:

```
nameserver 127.0.0.1
```

At this point, you can use a tool like `dig` to test whether you have
set up everything correctly.

`dig TXT rhine.ovgu.scionlab.` should return something like the following

```
; <<>> DiG 9.11.3-1ubuntu1.18-Ubuntu <<>> TXT rhine.ovgu.scionlab.
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15949
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 4

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 90b162220becaac8a2bf27fc8d760f847ba959d9c13042041a71f42b7d1efa9d8fbb753f2b4edd0f (good)
;; QUESTION SECTION:
;rhine.ovgu.scionlab.           IN      TXT

;; ANSWER SECTION:
rhine.ovgu.scionlab.    3600    IN      TXT     "scion=19-ffaa:1:fe4,127.0.0.1"
rhine.ovgu.scionlab.    3600    IN      RRSIG   TXT 13 3 3600 20221202042358 20221026180019 60887 . W3jQyDw2l1KkMF7QfB9AvvRjX+dZYEWLmlThiCFWfYMs6UcnF14uEGqb iDiPQHTFt3Uy8yS12SBJySuMjuHN6A==

;; ADDITIONAL SECTION:
.                       604800  IN      DNSKEY  257 3 13 cs4c2okGJCcuRcLFbQhSfd9EuoLGPCVGyxEGpW9xKtg1s5c81mhURajH GYHHRfRK7/OOdu37xEkDgzKPDxQfyg==
_rhinecert.             604800  IN      TXT     "rhineCert Ed25519 MIIBBzCBuqADAgECAgEBMAUGAytlcDAbMRkwFwYDVQQDExBSSElORSBFWEFNUExFIENBMB4XDTIyMDkwNzE2NTQwOVoXDTIzMDgyOTE2NTQwOVowGzEZMBcGA1UEAxMQUkhJTkUgRVhBTVBMRSBDQTAqMAUGAytlcAMhAOyCUoevfoatTpp0xZ3SQ1i3KqzrlSo6LeKFbG7CgCfGoyMwITAOBgNVHQ8BAf8EBAMCAoQwD" "wYDVR0TAQH/BAUwAwEB/zAFBgMrZXADQQDAP8Qut5bRjbbU11yg5oAgn/oJKeQvXYtwOPy7xIlAkbGDHwmLjlFpyizzwsYKcDrlNgGXoeD4J47ucAJFkioB"
.                       604800  IN      RRSIG   DNSKEY 15 0 604800 20221202042358 20221026180019 9991 . kepAcFiomVlywj1H4L42Fn05+6I1MowOXDR8TBJs/XE9DqOR74uBA7Fu AQlHl4QpPX5lCokyl0rSQpRGXqJ0CQ==

;; Query time: 442 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Tue Nov 01 14:55:39 UTC 2022
;; MSG SIZE  rcvd: 800
```


## DNS-enabled SCION apps

With the merging of [PR #230](https://github.com/netsec-ethz/scion-apps/pull/230), the DNS-enabled SCION apps can now directly be built from the [upstream repository](https://github.com/netsec-ethz/scion-apps) via `make -j build`.

## Service Names

A few public services are available under their domain names in SCIONLab. For example, try the following:

* `scion-sensorfetcher -s sensorserver.ethz.scionlab:42003`
* `scion-bat HEAD https://netsys.ovgu.de` (this should even work without a working `scion-sdns` deployment, because the `netsys.ovgu.de` domain has a "real" DNS TXT record with its scion address too)

### Bandwidth Test Servers

There are [Bandwidth Testers](https://docs.scionlab.org/content/apps/bwtester.html) available under the following DNS names:

- `bwtester.frankfurt.aws.scionlab`
- `bwtester.ireland.aws.scionlab`
- `bwtester.virginia.aws.scionlab`
- `bwtester.ohio.aws.scionlab`
- `bwtester.oregon.aws.scionlab`
- `bwtester.singapore.aws.scionlab`
- `bwtester1.ethz.scionlab`
- `bwtester2.ethz.scionlab`
- `bwtester3.ethz.scionlab`
- `bwtester.ap.ethz.scionlab`
- `bwtester1.switch.scionlab`
- `bwtester2.switch.scionlab`
- `bwtester.valencia.eu.scionlab`
- `bwtester.daejeon.korea.scionlab`
- `bwtester.ku.korea.scionlab`

They are all reachable under port 30100. You will need to specify the
parameters for each bandwidth experiment. For demonstration purposes, something like the following might suffice:

```
scion-bwtestclient -s bwtester.frankfurt.aws.scionlab:30100 -cs 1,1000,125,1Mbps -sc 1,1000,125,1Mbps
```

## Troubleshooting

If you get the following error:
```
2022/09/07 16:49:31 host not found: 'bwtester.frankfurt.aws.scionlab'
```

There are several possible causes:

- You might be using an "old" `scion-bwtestclient` binary that is already
  installed on the host. Check if `dig TXT
  bwtester.frankfurt.aws.scionlab.` returns a `TXT` record with a `scion`
  entry like in the example above. If that works fine, your
  `scion-bwtestclient` binary does not yet support DNS.
- Your `scion-sdns` version is not running or misconfigured. Check its
  debug output.
- The experimental autoritative name servers are both offline. Check if either of them
  can be reached under their addresses via `scion ping
  "19-ffaa:1:fe4,127.0.0.1"` and `scion ping 17-ffaa:1:1008,[127.0.0.1]` respectively.

If you get the following error:
```
2022/09/19 16:57:52 no path to 19-ffaa:1:140
```

There are two possibilities:

- The service might be offline
- Your scion host has no connection to SCIONLab
