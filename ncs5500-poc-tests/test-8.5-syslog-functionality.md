# Test 8.5: Syslog functionality

```erlang
logging console disable
logging monitor disable
logging buffered 10000000
logging facility {{ SYSLOG.FACILITY }}
{%   for SERVER in SYSLOG.SERVER_LIST %}
logging {{ SERVER }} vrf {{ MGMT_VRF_NAME }}
{%   endfor %}
logging source-interface {{ SYSLOG.SOURCE_INTERFACE }}
```

