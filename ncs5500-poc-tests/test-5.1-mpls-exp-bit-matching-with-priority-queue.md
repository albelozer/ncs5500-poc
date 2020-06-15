# Test 5.1: MPLS EXP bit matching with priority queue

{% embed url="https://xrdocs.io/ncs5500/tutorials/ncs5500-qos-part-1-understanding-packet-buffering/" %}

{% embed url="https://xrdocs.io/ncs5500/tutorials/ncs5500-qos-part-2-verifying-buffering/" %}

{% embed url="https://www.cisco.com/c/en/us/td/docs/iosxr/ncs5500/qos/71x/b-qos-cg-ncs5500-71x.html" %}

```erlang
class-map match-any CM-EXP-5
 match mpls experimental topmost 5
 end-class-map
!
class-map match-any CM-TC-5
 match traffic-class 5 
 end-class-map
!
policy-map PM-QOS-IN
 class CM-EXP-5
  set traffic-class 5
!
policy-map PM-QUEUING-OUT
 class CM-TC-5
  priority level 1
 !
 class class-default
 !
 end-policy-map
!
```

## Verification

```erlang
show qos interface {{ IFACE }} output
```



