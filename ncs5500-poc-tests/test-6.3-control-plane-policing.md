# Test 6.3: Control plane policing

{% embed url="https://www.cisco.com/c/en/us/td/docs/iosxr/ncs5500/ip-addresses/71x/b-ip-addresses-cg-ncs5500-71x/b-ip-addresses-cg-ncs5500-71x\_chapter\_01001.html" %}

```text
show lpts pifib hardware police
show lpts pifib hardware entry brief location node-id | inc SSH
show lpts punt statistics location 0/0/CPU0
```

## Example

```text
lpts punt police
 interface TenGigE0/0/0/8/0
  mcast rate 1000
  bcast rate 1000
 !
 mcast rate 1000
 bcast rate 1000
 protocol arp rate 700
 protocol cdp rate 700
 !
 domain ACCESS
  mcast rate 5000
  bcast rate 5000
 !
!
lpts pifib hardware domain ACCESS
 interface TenGigE0/0/0/8/1
!
lpts punt police location 0/0/CPU0
 protocol arp rate 500
 protocol cdp rate 500
!
lpts punt police location 0/4/CPU0
!
```

