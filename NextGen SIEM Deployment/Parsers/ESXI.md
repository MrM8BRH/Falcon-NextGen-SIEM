```yaml
sources:
  syslog_ESXI_1515: 
    type: syslog 
    mode: udp 
    port: 1515 
    sink: ESXI 
    transforms: 
     - type: regex_filter 
       mode: include 
       pattern: vpxd|vpxd-main|applmgmt-audit|vmaffd| vpxd-svcs-access|vmkernal|hostd|vmauthd 

sinks: 
  ESXI: 
    type: hec 
    token: **** 
    url: ****
```
