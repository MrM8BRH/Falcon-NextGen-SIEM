```yaml
sources: 
  cymetricx_logs: 
    type: file 
    include: /var/log/cymetricx/activitiylogs.csv 
    sink: cymetricx 
sinks: 
  cymetricx: 
    type: hec 
    token: *** 
    url: *** 
```
