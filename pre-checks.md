# Pre-checks

{% embed url="https://www.cisco.com/c/en/us/td/docs/iosxr/ncs5500/system-setup/71x/b-system-setup-cg-ncs5500-71x/b-system-setup-cg-ncs5500-71x\_chapter\_0100.html" %}

## Power supply

2xPS modules for NCS5504  
3xPS modules for NCS5508

## IOS XR Software

Boxes have arrived with 7.1.1.

## FPD Firmware

```text
show hw-module fpd
```

## TCAM resource allocation \(optional\)

{% hint style="info" %}
Required for Test 2.3.
{% endhint %}

```text
hw-module fib ipv4 scale internet-optimized
```

{% hint style="warning" %}
A reload is required to apply the profile.
{% endhint %}

