# Introduction

{% hint style="info" %}
Only basic configuration is provided. Protocol tuning and hardening should be addressed separately.
{% endhint %}

## IOS XR Introduction

1. Two stage commit
2. Commit confirmed
3. Commit best-effort
4. Rollback
5. Commit list
6. Load
7. Abort/Clear/root
8. Alias
9. pwd
10. Submode

## IOS XR show running-config

```text
RP/0/RP0/CPU0:NCS-540-A#sh run | ?
  begin    Begin with the line that matches
  exclude  Exclude lines that match
  file     Save the configuration
  include  Include lines that match
  utility  A set of common unix utilities
  xml      Show configuration in YANG xml tree
  <cr>     Shows current operating configuration
```

```text
RP/0/RP0/CPU0:NCS-540-A#sh run | utility ?
  cut     Cut out selected fields of each line of a file
  egrep   Extended regular expression grep
  fgrep   Fixed string expression grep
  head    Show set of lines/characters from the top of a file
  less    Fixed string pattern matching
  more    Paging Utility More
  script  Launch a script for post processing
  sort    Sort, merge, or sequence-check text files
  tail    Copy the last part of files
  uniq    Report or filter out repeated lines in a file
  wc      Counting lines/words/characters of a file
  xargs   Construct argument list(s) and invoke a program
```

```markup
RP/0/RP0/CPU0:NCS540-01#sh running-config router bgp | xml openconfig
Tue Mar 24 11:19:36.163 UTC
 <data>
  <bgp xmlns="http://cisco.com/ns/yang/Cisco-IOS-XR-ipv4-bgp-cfg">
   <instance>
    <instance-name>default</instance-name>
    <instance-as>
     <as>0</as>
     <four-byte-as>
      <as>65400</as>
      <bgp-running></bgp-running>
      <default-vrf>
       <global>
        <router-id>1.1.1.1</router-id>
        <global-afs>
         <global-af>
          <af-name>vpnv4-unicast</af-name>
          <enable></enable>
         </global-af>
        </global-afs>
       </global>
      </default-vrf>
...
```

## Base Configuration

### YAML Variables

```yaml
RID_IP_ADDRESS: "172.16.1.11"
MGMT_VRF_NAME: "default"
ISIS:
  PROCESS_NAME: "CORE"
  NET: ~
# ISIS_NET: 1720.1600.1013
NNI_IFACE_LIST:
  - "GigabitEthernet0/0/0/0"
  - "GigabitEthernet0/0/0/1"
  - "GigabitEthernet0/0/0/2"
  - "GigabitEthernet0/0/0/3"
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
 interface {{ IFACE }}
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

