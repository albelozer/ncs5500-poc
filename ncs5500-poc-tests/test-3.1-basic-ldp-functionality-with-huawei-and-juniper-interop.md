# Test 3.1: Basic LDP functionality with Huawei and Juniper interop

## **LDP configuration template**

```erlang
{% for DEVICE in DEVICE_LIST %}
ipv4 access-list {{ ACL_ACCEPT_LDP }}
 permit ipv4 {{ LOOPBACK_SUBNET }} any
!
mpls ldp
 router-id {{ DEVICE.L0.IPV4 }}
 address-family ipv4
  label
   local
    allocate for host-routes
   !
   remote
    accept
{%   for LDP_PEER in DEVICE.LDP_PEER_LIST %}
     from {{ LDP_PEER }}:0 for {{ ACL_ACCEPT_LDP }}
{%   endfor %}
    !
   !
  !
 !
!
!---------------------------------------------------------------------------------
{% endfor %}
```

## **LDP configuration example**

```coffeescript
mpls ldp
 log
  neighbor
  graceful-restart
  session-protection
 !
 graceful-restart
 mldp
 !
 router-id 172.16.1.13
 session protection
 address-family ipv4
  label
   local
    allocate for host-routes
   !
   remote
    accept
     from 172.16.1.11:0 for ACL-ACCEPT-LDP
     from 172.16.1.14:0 for ACL-ACCEPT-LDP
     from 172.16.1.21:0 for ACL-ACCEPT-LDP
     from 172.16.1.22:0 for ACL-ACCEPT-LDP
    !
   !
  !
 !
!
```

## **LDP verification**

```erlang
RP/0/0/CPU0:P3#show mpls ldp neighbor
Mon Jun  8 10:07:04.928 UTC

Peer LDP Identifier: 172.16.1.21:0
  TCP connection: 172.16.1.21:62345 - 172.16.1.13:646
  Graceful Restart: No
  Session Holdtime: 180 sec
  State: Oper; Msgs sent/rcvd: 1096/1093; Downstream-Unsolicited
  Up time: 15:33:32
  LDP Discovery Sources:
    IPv4: (1)
      GigabitEthernet0/0/0/3
    IPv6: (0)
  Addresses bound to this peer:
    IPv4: (3)
      172.16.1.21    172.16.32.11   172.16.32.12
    IPv6: (0)

Peer LDP Identifier: 172.16.1.14:0
  TCP connection: 172.16.1.14:19907 - 172.16.1.13:646
  Graceful Restart: No
  Session Holdtime: 180 sec
  State: Oper; Msgs sent/rcvd: 1096/1098; Downstream-Unsolicited
  Up time: 15:33:32
  LDP Discovery Sources:
    IPv4: (1)
      GigabitEthernet0/0/0/1
    IPv6: (0)
  Addresses bound to this peer:
    IPv4: (7)
      172.16.1.14    172.16.32.1    172.16.32.7    172.16.32.8
      172.16.32.24   172.16.32.26   172.16.32.28
    IPv6: (0)

Peer LDP Identifier: 172.16.1.22:0
  TCP connection: 172.16.1.22:21868 - 172.16.1.13:646
  Graceful Restart: No
  Session Holdtime: 180 sec
  State: Oper; Msgs sent/rcvd: 1093/1097; Downstream-Unsolicited
  Up time: 15:33:32
  LDP Discovery Sources:
    IPv4: (1)
      GigabitEthernet0/0/0/2
    IPv6: (0)
  Addresses bound to this peer:
    IPv4: (3)
      172.16.1.22    172.16.32.15   172.16.32.16
    IPv6: (0)
```

```erlang
RP/0/0/CPU0:P3#sh mpl ldp parameters
Mon Jun  8 10:13:56.300 UTC

LDP Parameters:
  Role: Active
  Protocol Version: 1
  Router ID: 172.16.1.13
  Null Label:
    IPv4: Implicit
  Session:
    Hold time: 180 sec
    Keepalive interval: 60 sec
    Backoff: Initial:15 sec, Maximum:120 sec
    Global MD5 password: Disabled
  Discovery:
    Link Hellos:     Holdtime:15 sec, Interval:5 sec
    Targeted Hellos: Holdtime:90 sec, Interval:10 sec
    Quick-start: Enabled (by default)
    Transport address:
      IPv4: 172.16.1.13
  Graceful Restart:
    Disabled
  NSR: Disabled, Not Sync-ed
  Timeouts:
    Housekeeping periodic timer: 10 sec
    Local binding: 300 sec
    Forwarding state in LSD: 15 sec
  Delay in AF Binding Withdrawl from peer: 180 sec
  Max:
    1500 interfaces (1200 attached, 300 TE tunnel), 2000 peers
  OOR state
    Memory: Normal
```

```erlang
RP/0/0/CPU0:P3#sh mpl ldp bindings brief 
Fri Jun 12 09:26:50.262 UTC

Prefix              Local      Advertised  Remote Bindings
                    Label        (peers)       (peers)    
------------------  ---------  ----------  ---------------
1.1.1.1/32          24001               4                0
172.16.1.1/32       24009               4                4
172.16.1.11/32      24003               4                4
172.16.1.12/32      24002               4                4
172.16.1.13/32      ImpNull             4                4
172.16.1.14/32      24007               4                4
172.16.1.21/32      24005               4                4
172.16.1.22/32      24006               4                4
172.16.1.23/32      24000               4                4
172.16.1.24/32      24004               4                4
172.16.1.25/32      24008               4                4
```

```erlang
RP/0/0/CPU0:P3#sh mpl ldp bindings 
Fri Jun 12 09:50:29.764 UTC

1.1.1.1/32, rev 8
        Local binding: label: 24001
        No remote bindings
172.16.1.1/32, rev 37
        Local binding: label: 24009
        Remote bindings: (4 peers)
            Peer                Label    
            -----------------   ---------
            172.16.1.11:0       36      
            172.16.1.14:0       24018   
            172.16.1.21:0       24009   
            172.16.1.22:0       24012   
172.16.1.11/32, rev 10
        Local binding: label: 24003
        Remote bindings: (4 peers)
            Peer                Label    
            -----------------   ---------
            172.16.1.11:0       ImpNull 
            172.16.1.14:0       24005   
            172.16.1.21:0       24005   
            172.16.1.22:0       24006   
172.16.1.12/32, rev 9
        Local binding: label: 24002
        Remote bindings: (4 peers)
            Peer                Label    
            -----------------   ---------
            172.16.1.11:0       16      
            172.16.1.14:0       24000   
            172.16.1.21:0       24004   
            172.16.1.22:0       24005   
172.16.1.13/32, rev 2
        Local binding: label: ImpNull
        Remote bindings: (4 peers)
            Peer                Label    
            -----------------   ---------
            172.16.1.11:0       26      
            172.16.1.14:0       24001   
            172.16.1.21:0       24003   
            172.16.1.22:0       24004   
172.16.1.14/32, rev 31
        Local binding: label: 24007
        Remote bindings: (4 peers)
            Peer                Label    
            -----------------   ---------
            172.16.1.11:0       32      
            172.16.1.14:0       ImpNull 
            172.16.1.21:0       24007   
            172.16.1.22:0       24010   
172.16.1.21/32, rev 27
        Local binding: label: 24005
        Remote bindings: (4 peers)
            Peer                Label    
            -----------------   ---------
            172.16.1.11:0       30      
            172.16.1.14:0       24006   
            172.16.1.21:0       ImpNull 
            172.16.1.22:0       24003   
172.16.1.22/32, rev 29
        Local binding: label: 24006
        Remote bindings: (4 peers)
            Peer                Label    
            -----------------   ---------
            172.16.1.11:0       31      
            172.16.1.14:0       24007   
            172.16.1.21:0       24006   
            172.16.1.22:0       ImpNull 
172.16.1.23/32, rev 7
        Local binding: label: 24000
        Remote bindings: (4 peers)
            Peer                Label    
            -----------------   ---------
            172.16.1.11:0       23      
            172.16.1.14:0       24002   
            172.16.1.21:0       24001   
            172.16.1.22:0       24001   
172.16.1.24/32, rev 12
        Local binding: label: 24004
        Remote bindings: (4 peers)
            Peer                Label    
            -----------------   ---------
            172.16.1.11:0       22      
            172.16.1.14:0       24003   
            172.16.1.21:0       24000   
            172.16.1.22:0       24000   
172.16.1.25/32, rev 35
        Local binding: label: 24008
        Remote bindings: (4 peers)
            Peer                Label    
            -----------------   ---------
            172.16.1.11:0       35      
            172.16.1.14:0       24016   
            172.16.1.21:0       24008   
            172.16.1.22:0       24011
```

```erlang
RP/0/0/CPU0:P3#sh mpl ldp forwarding         
Fri Jun 12 08:47:28.833 UTC

Codes: 
  - = GR label recovering, (!) = LFA FRR pure backup path 
  {} = Label stack with multi-line output for a routing path
  G = GR, S = Stale, R = Remote LFA FRR backup

Prefix          Label   Label(s)       Outgoing     Next Hop            Flags
                In      Out            Interface                        G S R
--------------- ------- -------------- ------------ ------------------- -----
1.1.1.1/32      24001   24004          Gi0/0/0/1    172.16.32.1         G    
172.16.1.1/32   24009   24018          Gi0/0/0/1    172.16.32.1         G    
172.16.1.11/32  24003   ImpNull        Gi0/0/0/0    172.16.32.3              
172.16.1.12/32  24002   16             Gi0/0/0/0    172.16.32.3              
                        24000          Gi0/0/0/1    172.16.32.1         G    
172.16.1.14/32  24007   ImpNull        Gi0/0/0/1    172.16.32.1         G    
172.16.1.21/32  24005   ImpNull        Gi0/0/0/3    172.16.32.12             
172.16.1.22/32  24006   ImpNull        Gi0/0/0/2    172.16.32.15             
172.16.1.23/32  24000   24002          Gi0/0/0/1    172.16.32.1         G    
172.16.1.24/32  24004   24003          Gi0/0/0/1    172.16.32.1         G    
172.16.1.25/32  24008   24016          Gi0/0/0/1    172.16.32.1         G   
```

```erlang
RP/0/0/CPU0:P3#show mpls label table summary
Fri Jun 12 09:58:19.522 UTC
Application                  Count  
---------------------------- -------
LSD(A)                       4
LDP(A)                       10
---------------------------- -------
TOTAL                        14
```

