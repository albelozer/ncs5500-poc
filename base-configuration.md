# Base Configuration

### YAML Variables

```yaml
---
RID_IP_ADDRESS: "172.16.1.11"
MGMT_VRF_NAME: "default"
ISIS:
  PROCESS_NAME: "CORE"
  NET: ~
# ISIS_NET: 1720.1600.1013
NNI_IFACE_LIST:
  - NAME: "GigabitEthernet0/0/0/0"
  - NAME: "GigabitEthernet0/0/0/1"
  - NAME: "GigabitEthernet0/0/0/2"
  - NAME: "GigabitEthernet0/0/0/3"
BGP:
  ASN: "100"
  RR_IP_ADDRESS: "172.16.1.1"
```

### IPv4 addressing

### IPv6 addressing

### IS-IS base configuration

```erlang
router isis {{ ISIS.PROCESS_NAME }}
 is-type level-2-only
 net 49.{{ ISIS.NET }}.00
 log adjacency changes
 address-family ipv4 unicast
  metric-style wide
  router-id Loopback0
  mpls ldp auto-config
 !
 address-family ipv6 unicast
  metric-style wide
  router-id Loopback0
 !
 interface Loopback0
  passive
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
 {% for IFACE in NNI_IFACE_LIST %}
 interface {{ IFACE.NAME }}
  point-to-point
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
 {% endfor %}
!
```

### MPLS LDP and mLDP

```erlang
mpls ldp
 log
  neighbor
  graceful-restart
  session-protection
 !
 graceful-restart
 mldp
 !
 router-id {{ RID_IP_ADDRESS }}
 session protection
 address-family ipv4
  label
   local
    allocate for host-routes
   !
  !
 !
!
```

### BGP

```erlang
router bgp {{ BGP.ASN }}
 bgp router-id {{ RID_IP_ADDRESS }}
 bgp graceful-restart
 bgp log neighbor changes detail
 address-family ipv4 unicast
 !
 neighbor {{ BGP.RR_IP_ADDRESS }}
  remote-as {{ BGP.ASN }}
  update-source Loopback0
  address-family ipv4 unicast
  !
 !
!
```

### PIM SM in the core

```erlang
multicast-routing
 address-family ipv4
  interface all enable
 !
!
```

### SSH and telnet

```erlang
telnet vrf {{ MGMT_VRF_NAME  }} ipv4 server max-servers {{ MAX_TELNET }}
!
ssh server logging
ssh server disable hmac hmac-sha1
ssh server algorithms cipher aes256-ctr aes128-gcm@openssh.com aes256-gcm@openssh.com
ssh server algorithms host-key ecdsa-nistp256 ecdsa-nistp384 ecdsa-nistp521
ssh server algorithms key-exchange ecdh-sha2-nistp521 ecdh-sha2-nistp384 ecdh-sha2-nistp256
ssh server v2
ssh server vrf {{ MGMT_VRF_NAME }} ipv4 access-list {{ SSH_ACL }}
```

### Management plane protection

```erlang
control-plane
 management-plane
  inband
   interface all
    allow SSH
    allow SNMP
    allow Telnet
    allow NETCONF
   !
  !
 !
!
```

