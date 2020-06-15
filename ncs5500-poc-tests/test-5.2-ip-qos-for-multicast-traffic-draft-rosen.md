# Test 5.2: IP QoS for multicast traffic \(Draft-Rosen\)

```erlang
class-map match-any CM-DSCP-AF41
 match dscp af41 
 end-class-map
!
class-map match-any CM-TC-4
 match traffic-class 4
 end-class-map
!
policy-map PM-QOS-IN
 class CM-EXP-5
  set traffic-class 5
 !
 class CM-DSCP-AF41
  set traffic-class 4
 !
 class class-default
 ! 
 end-policy-map
!
policy-map PM-QUEUING-OUT
 class CM-TC-5
  priority level 1 
 !
 class CM-TC-4
  bandwidth remaining percent 50
 !
 class class-default
 ! 
 end-policy-map
!
```

