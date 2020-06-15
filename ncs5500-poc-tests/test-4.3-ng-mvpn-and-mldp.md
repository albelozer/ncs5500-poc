# Test 4.3: NG-MVPN and mLDP

## Configuration

{% hint style="info" %}
There's no configuration required for P routers other than enabling mLDP.
{% endhint %}

```text
mpls ldp
 mldp
!
```

## Verification

```erlang
RP/0/0/CPU0:P3#show mpls mldp neighbor
Mon Jun 15 09:10:11.634 UTC
mLDP neighbor database
 MLDP peer ID      : 172.16.1.14:0, uptime 5d19h Up, 
  Capabilities     : GR, Typed Wildcard FEC, P2MP, MP2MP
  Target Adj       : No
  Upstream count   : 0
  Branch count     : 0
  LDP GR           : Enabled
                   : Instance: 1
  Label map timer  : never
  Policy filter in : 
  Path count       : 1
  Path(s)          : 172.16.32.1       GigabitEthernet0/0/0/1 LDP 
  Adj list         : 172.16.32.1       GigabitEthernet0/0/0/1
  Peer addr list   : 172.16.32.1      
                   : 172.16.32.7      
                   : 172.16.32.8      
                   : 172.16.32.28     
                   : 172.16.32.26     
                   : 172.16.32.24     
                   : 172.16.1.14      

 MLDP peer ID      : 172.16.1.21:0, uptime 5d20h Up, 
  Capabilities     : Typed Wildcard FEC, P2MP, MP2MP
  Target Adj       : No
  Upstream count   : 0
  Branch count     : 0
  Label map timer  : never
  Policy filter in : 
  Path count       : 1
  Path(s)          : 172.16.32.12      GigabitEthernet0/0/0/3 LDP 
  Adj list         : 172.16.32.12      GigabitEthernet0/0/0/3
  Peer addr list   : 172.16.1.21      
                   : 172.16.32.12     
                   : 172.16.32.11     
                   : 172.17.1.0       

 MLDP peer ID      : 172.16.1.22:0, uptime 5d20h Up, 
  Capabilities     : Typed Wildcard FEC, P2MP, MP2MP
  Target Adj       : No
  Upstream count   : 0
  Branch count     : 0
  Label map timer  : never
  Policy filter in : 
  Path count       : 1
  Path(s)          : 172.16.32.15      GigabitEthernet0/0/0/2 LDP 
  Adj list         : 172.16.32.15      GigabitEthernet0/0/0/2
  Peer addr list   : 172.16.1.22      
                   : 172.16.32.16     
                   : 172.16.32.15     
                   : 172.17.1.2
```

```text
show mpls mldp bindings
show mpls mldp trace
sh mpls forwarding p2mp
```



