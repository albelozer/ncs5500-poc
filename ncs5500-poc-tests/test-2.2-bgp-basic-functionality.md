# Test 2.2: BGP basic functionality

## **BGP configuration template**

```erlang
{% for DEVICE in DEVICE_LIST %}
router bgp {{ BGP.ASN }}
 bgp router-id {{ DEVICE.L0.IPV4 }}
 bgp graceful-restart
 bgp log neighbor changes detail
{%   for AF in BGP.AF_LIST %}
 address-family {{ AF.NAME }}
 !
{%   endfor %}
{%   for RR in BGP.RR_LIST %}
 neighbor {{ RR.ADDRESS }}
  remote-as {{ BGP.ASN }}
  update-source Loopback0
  address-family {{ RR.AF }}
  !
 !
{%   endfor %}
----------------------------------------------------------------------------------
{% endfor %}
```

## **BGP configuration example**

```erlang
router bgp 100
 bgp router-id 172.16.1.13
 address-family ipv4 unicast
 !
 neighbor 172.16.1.1
  remote-as 100
  update-source Loopback0
  address-family ipv4 unicast
  !
 !
!
```

## **BGP verification**

```erlang
RP/0/0/CPU0:P3#sh bgp all all summary
Mon Jun  8 10:03:39.682 UTC

Address Family: IPv4 Unicast
----------------------------

BGP router identifier 172.16.1.13, local AS number 100
BGP generic scan interval 60 secs
Non-stop routing is enabled
BGP table state: Active
Table ID: 0xe0000000   RD version: 2
BGP main routing table version 2
BGP NSR Initial initsync version 2 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0
BGP scan interval 60 secs

BGP is operating in STANDALONE mode.


Process       RcvTblVer   bRIB/RIB   LabelVer  ImportVer  SendTblVer  StandbyVer
Speaker               2          2          2          2           2           0

Neighbor        Spk    AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down  St/PfxRcd
172.16.1.1        0   100    6037    5487        2    0    0    3d19h          0
```

