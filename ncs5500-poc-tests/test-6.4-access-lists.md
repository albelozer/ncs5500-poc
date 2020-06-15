# Test 6.4: Access lists

{% embed url="https://www.cisco.com/c/en/us/td/docs/iosxr/ncs5500/ip-addresses/71x/b-ip-addresses-cg-ncs5500-71x/b-ip-addresses-cg-ncs5500-71x\_chapter\_0111.html" %}

```erlang
object-group network ipv4 DENY-ICMP
!
object-group network ipv4 ALLOW-ICMP
!
ipv4 access-list ACL-ICMP-TEST
 10 permit icmp net-group ALLOW-ICMP any
 20 deny icmp net-group DENY-ICMP any
 30 permit ipv4 any any
!
```

