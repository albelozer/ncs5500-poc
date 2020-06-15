# Test 8.3: SNMPv3 functionality

```erlang
ipv4 access-list ACL-SNMP
 10 permit ipv4 host {{ SNMP_SERVER }} any
!
snmp-server ifmib ifalias long
snmp-server ifindex persist
snmp-server ifmib stats cache
snmp-server trap link ietf
snmp-server engineID local 010000097014
snmp-server vrf Mgt
 host {{ SNMP-SERVER }} traps version 3 auth CISCO
!
snmp-server user CISCO NMS v3 auth md5 encrypted 072C087F6D26485744
snmp-server community encrypted 00273A352774 RW IPv4 SNMP
snmp-server group NMS v3 auth notify v1default write v1default IPv4 ACL-SNMP
snmp-server queue-length 200
snmp-server traps bfd
snmp-server traps ntp
snmp-server traps snmp
snmp-server traps snmp linkup
snmp-server traps snmp linkdown
snmp-server traps config
snmp-server traps syslog
snmp-server traps system
snmp-server trap-source Loopback1
```

