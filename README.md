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

