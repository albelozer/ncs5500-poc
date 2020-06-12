# Base Configuration

### YAML Variables

```yaml
---
MGMT_VRF_NAME: "default"
MAX_TELNET: 20
SSH_ACL: "ACL-SSH-IN"
SYSLOG:
  FACILITY: "local5"
  SOURCE_INTERFACE: "Loopback0"
  SERVER_LIST:
    - "172.1.1.1"
ISIS:
  PROCESS_NAME: "CORE"
BGP:
  ASN: "100"
  RR_LIST:
    - ADDRESS: "172.16.1.1"
      AF: "ipv4 unicast"
    - ADDRESS: "2001:db8::1"
      AF: "ipv6 unicast"
  AF_LIST:
    - NAME: "ipv4 unicast"
      ACTIVE: True
    - NAME: "ipv6 unicast"
      ACTIVE: True
MPLD: True
NTP:
  SERVERS:
    - "172.16.0.1"
LOGGING:
  SERVERS:
    - "172.16.0.2"
DEVICE_LIST:
  - NAME: "P3"
    ID: 13
    L0:
      IPV4: "172.16.1.13"
      IPV6: ~
    ISIS:
      NET: "1720.1600.1013"
    NNI_IFACE_LIST:
      - NAME: "GigabitEthernet0/0/0/0"
        IP:
          IPV4: ~
          IPV6: ~
      - NAME: "GigabitEthernet0/0/0/1"
        IP:
          IPV4: ~
          IPV6: ~
  - NAME: "P4"
    ID: 14
    L0:
      IPV4: "172.16.1.14"
      IPV6: ~
    ISIS:
      NET: "1720.1600.1014"
    NNI_IFACE_LIST:
      - NAME: "GigabitEthernet0/0/0/0"
        IP:
          IPV4: ~
          IPV6: ~
      - NAME: "GigabitEthernet0/0/0/1"
        IP:
          IPV4: ~
          IPV6: ~
```

### Full template for NCS 5500 P-routers

```erlang
{% for DEVICE in DEVICE_LIST %}
hostname {{ DEVICE.NAME }}
!
telnet vrf {{ MGMT_VRF_NAME }} ipv4 server max-servers {{ MAX_TELNET }}
!
logging console disable
logging monitor disable
logging buffered 10000000
logging facility {{ SYSLOG.FACILITY }}
{%   for SERVER in SYSLOG.SERVER_LIST %}
logging {{ SERVER }} vrf {{ MGMT_VRF_NAME }}
{%   endfor %}
logging source-interface {{ SYSLOG.SOURCE_INTERFACE }}
!
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
interface Loopback0
 ipv4 address {{ DEVICE.L0.IPV4 }}/32
 ipv6 address {{ DEVICE.L0.IPV6 }}/128
!
{%   for IFACE in DEVICE.NNI_IFACE_LIST %}
interface {{ IFACE.NAME }}
 ipv4 address {{ IFACE.IP.IPV4 }}
 ipv6 address {{ IFACE.IP.IPV6 }}
!
{%   endfor %}
router isis {{ ISIS.PROCESS_NAME }}
 set-overload-bit on-startup 600
 is-type level-2-only
 net {{ DEVICE.ISIS.NET }}
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
{%   for IFACE in DEVICE.NNI_IFACE_LIST %}
 interface {{ IFACE.NAME }}
  point-to-point
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
{%   endfor %}
!
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
!
mpls ldp
 log
  neighbor
  graceful-restart
  session-protection
 !
 graceful-restart
 mldp
 !
 router-id 172.16.1.{{ DEVICE.ID }}
 session protection
 address-family ipv4
  label
   local
    allocate for host-routes
   !
  !
 !
!
multicast-routing
 address-family ipv4
  interface all enable
 !
!
ssh server logging
ssh server disable hmac hmac-sha1
ssh server algorithms cipher aes256-ctr aes128-gcm@openssh.com aes256-gcm@openssh.com
ssh server algorithms host-key ecdsa-nistp256 ecdsa-nistp384 ecdsa-nistp521
ssh server algorithms key-exchange ecdh-sha2-nistp521 ecdh-sha2-nistp384 ecdh-sha2-nistp256
ssh server v2
ssh server vrf {{ MGMT_VRF_NAME }} ipv4 access-list {{ SSH_ACL }}
!---------------------------------------------------------------------------------
{% endfor %}
```





