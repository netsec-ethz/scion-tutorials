# Command-line AS Visualization

```shell
cd ~/go/src/github.com/netsec-ethz/scion/
```

```shell
python3 sub/scion-viz/python/as_viewer.py
usage: as_viewer.py [-h] [--addr ADDR] [-t] [-p] [-trc] [-crt] [-c] [-pp]
                    src_isdas [dst_isdas]
as_viewer.py: error: the following arguments are required: src_isdas
```

```shell
python3 sub/scion-viz/python/as_viewer.py -h
usage: as_viewer.py [-h] [--addr ADDR] [-t] [-p] [-trc] [-crt] [-c] [-pp]
                    src_isdas [dst_isdas]

SCION AS Path Viewer requires source and destination ISD-ASes to analyze.

positional arguments:
  src_isdas    ISD-AS source.
  dst_isdas    ISD-AS destination.

optional arguments:
  -h, --help   show this help message and exit
  --addr ADDR  ip address to bind to if not localhost
  -t           display source AS topology
  -p           display announced paths to destination
  -trc         display source TRC
  -crt         display source certificate chain
  -c           display source AS configuration
  -pp          display source path policy
```

```shell
python3 sub/scion-viz/python/as_viewer.py 1-1045 -t

SCION AS Viewer
(src) 1-1045 =======================> None (dst)
/run/shm/sciond/sd1-1045.sock
Starting sciond at /run/shm/sciond/sd1-1045.sock
----------------- AS TOPOLOGY: 1-1045
is_core_as: False
mtu: 1472
----------------- PATH SERVER:
Address: 10.0.2.15
Name: ps-1
Port: 31044
TTL: 0
----------------- CERTIFICATE SERVER:
Address: 10.0.2.15
Name: cs-1
Port: 31043
TTL: 0
----------------- BEACON SERVER:
Address: 10.0.2.15
Name: bs-1
Port: 31041
TTL: 0
----------------- SIBRA SERVER:
Address: 10.0.2.15
Name: sb-1
Port: 31045
TTL: 0
----------------- BORDER ROUTER:
Address: 10.0.2.15
Name: br-1
Port: 31042
Interface ID: 1
```

```shell
python3 sub/scion-viz/python/as_viewer.py 1-1045 2-24 -p

SCION AS Viewer
(src) 1-1045 =======================> 2-24 (dst)
/run/shm/sciond/sd1-1045.sock
Starting sciond at /run/shm/sciond/sd1-1045.sock
----------------- PATH 1
MTU: 1472
IPV4: 10.0.2.15
Port: 31042
Interfaces Len: 16
2-24 (1)
2-22 (4)
2-22 (1)
2-21 (2)
2-21 (1)
1-1 (4)
1-1 (3)
1-3 (1)
1-3 (2)
1-2 (1)
1-2 (4)
1-6 (1)
1-6 (2)
1-7 (1)
1-7 (39)
1-1045 (1)
----------------- PATH 2
MTU: 1472
IPV4: 10.0.2.15
Port: 31042
Interfaces Len: 16
2-24 (1)
2-22 (4)
2-22 (3)
4-41 (2)
4-41 (1)
1-1 (6)
1-1 (3)
1-3 (1)
1-3 (2)
1-2 (1)
1-2 (4)
1-6 (1)
1-6 (2)
1-7 (1)
1-7 (39)
1-1045 (1)
----------------- PATH 3
MTU: 1472
IPV4: 10.0.2.15
Port: 31042
Interfaces Len: 18
2-24 (1)
2-22 (4)
2-22 (2)
2-23 (2)
2-23 (3)
3-31 (3)
3-31 (1)
1-1 (5)
1-1 (3)
1-3 (1)
1-3 (2)
1-2 (1)
1-2 (4)
1-6 (1)
1-6 (2)
1-7 (1)
1-7 (39)
1-1045 (1)
----------------- PATH 4
MTU: 1472
IPV4: 10.0.2.15
Port: 31042
Interfaces Len: 18
2-24 (1)
2-22 (4)
2-22 (2)
2-23 (2)
2-23 (1)
2-21 (3)
2-21 (1)
1-1 (4)
1-1 (3)
1-3 (1)
1-3 (2)
1-2 (1)
1-2 (4)
1-6 (1)
1-6 (2)
1-7 (1)
1-7 (39)
1-1045 (1)
----------------- PATH 5
MTU: 1472
IPV4: 10.0.2.15
Port: 31042
Interfaces Len: 20
2-24 (1)
2-22 (4)
2-22 (1)
2-21 (2)
2-21 (3)
2-23 (1)
2-23 (3)
3-31 (3)
3-31 (1)
1-1 (5)
1-1 (3)
1-3 (1)
1-3 (2)
1-2 (1)
1-2 (4)
1-6 (1)
1-6 (2)
1-7 (1)
1-7 (39)
1-1045 (1)
```

```shell
python3 sub/scion-viz/python/as_viewer.py 1-1045 -h
usage: as_viewer.py [-h] [--addr ADDR] [-t] [-p] [-trc] [-crt] [-c] [-pp]
                    src_isdas [dst_isdas]

SCION AS Path Viewer requires source and destination ISD-ASes to analyze.

positional arguments:
  src_isdas    ISD-AS source.
  dst_isdas    ISD-AS destination.

optional arguments:
  -h, --help   show this help message and exit
  --addr ADDR  ip address to bind to if not localhost
  -t           display source AS topology
  -p           display announced paths to destination
  -trc         display source TRC
  -crt         display source certificate chain
  -c           display source AS configuration
  -pp          display source path policy
```

```shell
python3 sub/scion-viz/python/as_viewer.py 1-1045 -pp

SCION AS Viewer
(src) 1-1045 =======================> None (dst)
/run/shm/sciond/sd1-1045.sock
Starting sciond at /run/shm/sciond/sd1-1045.sock
/home/ubuntu/go/src/github.com/netsec-ethz/scion/gen/ISD1/AS1045/endhost/path_policy.yml
---
BestSetSize: 5
CandidatesSetSize: 20
HistoryLimit: 20
PropertyRanges:
  AvailableBandwidth: 0-100
  DelayTime: 0-60
  GuaranteedBandwidth: 0-100
  HopsLength: 1-10
  PeerLinks: 0-100
  TotalBandwidth: 0-100
PropertyWeights:
  AvailableBandwidth: 0
  DelayTime: 3
  Disjointness: 4
  ExpirationTime: 3
  GuaranteedBandwidth: 0
  HopsLength: 5
  LastSeenTime: 3
  LastSentTime: 3
  PeerLinks: 7
  TotalBandwidth: 0
UnwantedASes: 0-888,0-999
UpdateAfterNumber: 10
UpdateAfterTime: 3600
```

```shell
python3 sub/scion-viz/python/as_viewer.py 1-1045 -c

SCION AS Viewer
(src) 1-1045 =======================> None (dst)
/run/shm/sciond/sd1-1045.sock
Starting sciond at /run/shm/sciond/sd1-1045.sock
/home/ubuntu/go/src/github.com/netsec-ethz/scion/gen/ISD1/AS1045/endhost/as.yml
CertChainVersion: 0
MasterASKey: VrMpfgsWBxZZuhhSQoIUlg==
PropagateTime: 5
RegisterPath: true
RegisterTime: 5
```

```shell
python3 sub/scion-viz/python/as_viewer.py 1-1045 -crt

SCION AS Viewer
(src) 1-1045 =======================> None (dst)
/run/shm/sciond/sd1-1045.sock
Starting sciond at /run/shm/sciond/sd1-1045.sock
/home/ubuntu/go/src/github.com/netsec-ethz/scion/gen/ISD1/AS1045/endhost/certs/ISD1-AS1045-V0.crt
{
    "0": {
        "EncAlgorithm": "curve25519xsalsa20poly1305",
        "Signature": "VlJptnORObEmVQDvofOcN/2i83w2/j5vk/DKFMY508IC+fkWiyMZ/XQCp4sOWMc8cMiJHObdiI99GWz/wZXGDQ==",
        "IssuingTime": 1511289459,
        "SubjectSigKey": "Ushs6XHb/uypmGzcmHLA3Xay4FpsrSbrKJTWU0JeesQ=",
        "SignAlgorithm": "ed25519",
        "Subject": "1-1045",
        "TRCVersion": 0,
        "ExpirationTime": 1542825459,
        "SubjectEncKey": "4rQyFxRCx+eR7ygPYG96WQV9rKLl7RFY0lzXm1r2wg4=",
        "Comment": "AS Certificate",
        "Issuer": "1-1",
        "CanIssue": false,
        "Version": 0
    },
    "1": {
        "EncAlgorithm": "curve25519xsalsa20poly1305",
        "Signature": "G4K+h8C8flqOaGR6Upt483vm4agQeoY57XvK2Ljo7uI/7u3yUcd3rlONG/7YfrHGlYSrlcLcpzPFvu9knyMFDQ==",
        "IssuingTime": 1499177015,
        "SubjectSigKey": "rRfaiQA8CIPWpRD5pH6rDeadutCTB+Hi+6YRh2zPS1c=",
        "SignAlgorithm": "ed25519",
        "Subject": "1-1",
        "TRCVersion": 0,
        "ExpirationTime": 1530713015,
        "SubjectEncKey": "448SpoiC4OApC2UxyL8x9yFunPZ9n9Ms/AzQLMXQMmM=",
        "Comment": "Core AS Certificate",
        "Issuer": "1-1",
        "CanIssue": true,
        "Version": 0
    }
}
```

```shell
python3 sub/scion-viz/python/as_viewer.py 1-1045 -trc

SCION AS Viewer
(src) 1-1045 =======================> None (dst)
/run/shm/sciond/sd1-1045.sock
Starting sciond at /run/shm/sciond/sd1-1045.sock
/home/ubuntu/go/src/github.com/netsec-ethz/scion/gen/ISD1/AS1045/endhost/certs/ISD1-V0.trc
{
    "QuorumCAs": 3,
    "Signatures": {
        "1-1": "B89Z3cWTzW3j4r02b/zLMuAn1/PiF0meEa9/6/AITiyN3YGRpNNer5aD/uTziMX9IfPcUP9rEXlXKs8PCdVlBg=="
    },
    "GracePeriod": 18000,
    "RootRainsKey": "dns_srv_addr",
    "CreationTime": 1499177015,
    "PKILogs": {},
    "ISDID": 1,
    "CoreCAs": {
        "1-1": {
            "OfflineKey": "AX0kRbqJfdnfNcpC0XMSiQuLCqQJabp8HRi6BiAKPXo=",
            "OfflineKeyAlg": "Ed25519",
            "OnlineKey": "VJvKbgHOsYbHlr6SGGssszGQvAtu+zXCTAJkYOCPr38=",
            "OnlineKeyAlg": "Ed25519"
        }
    },
    "Quarantine": true,
    "Description": "ISD 1",
    "QuorumOwnTRC": 2,
    "RootCAs": {
        "CA1-1": "MIIC4DCCAcigAwIBAwIBATANBgkqhkiG9w0BAQsFADAQMQ4wDAYDVQQDDAVDQTEtMTAeFw0xNzA3MDQxNDAzMzVaFw0yMjA3MDMxNDAzMzVaMBAxDjAMBgNVBAMMBUNBMS0xMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxrPtywDofkLN4So+AhCDRsn0xeUtAp3zoa4onULpsSbdo5lj8p0nfafKplOJ7uP4txMbn8FtTcyau5W7BbQodXl/Xa6Elu4u9nyb6UgNpzmiUyCp/5TGRicv3MKSLpVZQukuj6weHLL2GGwz58qnygqoRzvsNMugwP46PZHKLmx7hg3IOR10MDDax7dtAMlzvJJWjLeMGytf75U6b5JFK2366oaDroQhhGh2TL228+3rcvMMcB/TVxHBrzGKE+uTI8XnspMGo42lN3ZzaOOvbacXH5s/Cdf3l6j6x1GdE0OHFLB2vRaKFpBN/BdW7HhDHqy/u+9/Vjh6eiOoDODMBQIDAQABo0UwQzASBgNVHRMBAf8ECDAGAQH/AgEBMA4GA1UdDwEB/wQEAwIBBjAdBgNVHQ4EFgQUchmrQ0w9YIrX1VjicXPcqVrouWswDQYJKoZIhvcNAQELBQADggEBAGCK0U5GFftXaRfaqgY7/dEeW3qk4zXPyCERd5la2KW8I1A4jUJ5yYZJTOovFe9Mi2NgyUG62NxN52mODNSRazZ+HxwmXVZtgUx9PEYMQVvFKoirsBbpxnx7Bnt1h9Fo0QVe1VGk0kYQ1sj/1/J4wperRDgy8bDBFB/JznY7z9S9HxfFNUgkmnMI53u/La4Yl18wB6sFNIqTeNwXNihzNWO1f3N9J9OMTjG0bjUMximNPDhUF7kCwIAXz3+C6besEqqCYq6mvcGchCL9H/zpw8vGJjwXPLbqpx0ewHlyPzEbCZqhy4kmHAodl5XU2ghuwCV/e7mSetuMlvSQPVtBXEY=",
        "CA1-3": "MIIC4DCCAcigAwIBAwIBATANBgkqhkiG9w0BAQsFADAQMQ4wDAYDVQQDDAVDQTEtMzAeFw0xNzA3MDQxNDAzMzVaFw0yMjA3MDMxNDAzMzVaMBAxDjAMBgNVBAMMBUNBMS0zMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAoVanBcuF4ypz1U9aCMgVLLbTMI356RlmK1g2njfvfvq1ElEt1WRyWRNPNSMx6xamD5OFedODgRdbpdYvcFK89cK4BbRLSSAZ13RpAmld0RnlRAxDvUKqUcfeqz5uiuQ5lVJ+zyyKJoSea/quooAyehGVEb0qo0g1EyffWGf/E4awkDXT+w6y14cGO9r4vrPiD+re5Ufs+ht5z/AwQ9xkK6Md5uaHoHRMd0GUkxy74ef9w5AoiysB+1/ZLoFKZyl1N+q+jUwx9C79zqicYDpBQWB3gFd19sq69ZlIyX76p6od2GSV8fvlsSD+MEdChfvPy1GFsoFE7zOBLYCXXXxkqwIDAQABo0UwQzASBgNVHRMBAf8ECDAGAQH/AgEBMA4GA1UdDwEB/wQEAwIBBjAdBgNVHQ4EFgQU91ybA6o68Yd/bd4wRe92F08xq7MwDQYJKoZIhvcNAQELBQADggEBADByHaGbqrI3Z9cO11xzBPzJwyS2AkFOFZ+bWPtGUwvdxoO/kFw3JTIjxD1GPM/5HQB86tjOBLKCCgwCQG/i/ZlwNU61v9aleFbJjFa5HnfuKxHxe5jtnspXjx6TBLV6KZUE82nNMnQYczlrUURmJ4gJ+9U4tLQVkmAERhq8HbCnO5wNeKkgPfKT1L3lRZP17Rh6WNQsOlUksJG6m/e8xR/BkZ0yHQuZPDUlcl7c94pL84CW1qJ7PcH3Mbi+AK/MnSDE4qjz1xCyoXvlklAOe6QrzYYLV9uvBpNJAldfr4vKHHdmWACWj03y+1pbHUB9edXZhv+skmQfpWBh4wxC/dQ=",
        "CA1-2": "MIIC4DCCAcigAwIBAwIBATANBgkqhkiG9w0BAQsFADAQMQ4wDAYDVQQDDAVDQTEtMjAeFw0xNzA3MDQxNDAzMzVaFw0yMjA3MDMxNDAzMzVaMBAxDjAMBgNVBAMMBUNBMS0yMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArS4eanUAEhbTtFi2zxo1YJYFG0IU/A7CD0WKyQL3TMK5HmAJY0rOth5MniTcx7OHCo4zcoMCTGXwtcfTHNnNyL3DKWoPpmjE0W8NbQpycgbcg8Wf/wBPXQ9eUKIlU7OUbIgWCOoxldr98A97N6sqxoo1u5EeNSUORYMiaEsbsgmKMxKj+jqwwJejTYL9sQLwktcXyW/sT27wBoiFeLWMpKEPp81qziub0dN42fSMgh0ZSBE68uAcwVAM3i6uT+4s+/5KoGehZ6KVjNT07TddSEcmc84Bb83oiKEThhc9xWyUOLVoIhQV/4PT6PA8fmpc1a6YfFRPSMj/Kzpl9clc9QIDAQABo0UwQzASBgNVHRMBAf8ECDAGAQH/AgEBMA4GA1UdDwEB/wQEAwIBBjAdBgNVHQ4EFgQUVRLUZoMbGkH6PXwIFgv578NwXi8wDQYJKoZIhvcNAQELBQADggEBAJ4oYQOIR4RLsHUGGYI9IOaki0Pvfg9DvD61wTBfkypsIc4wYI0TVeggzloxSG9DVSMuV3UYXYF5xCV24oY4CTzIazZXKsg200g/vICDdxO4hpQDgihzdUkGRRCT+zR2zi5lnWS1TZ59F0fdaGH8jEySjU8YdGv7NPKshtTFN2tx02ZoyJBa0S239L46eT7dJeJeAo3MSvNJDrwP+IE3gjL+nOLjMUreZzz94Ueqzgg7dlpFeyKJFvQvv2tUQ+SUQCUJkWZIlaOKEdMExxSKmEyI+grbLPqXtl9yVAr5WzjQQCNqRFEf/XucKLXFWV5sC+BaoWPJCcmpwbhidHAhmZo="
    },
    "Version": 0,
    "QuorumEEPKI": 2
}
```
