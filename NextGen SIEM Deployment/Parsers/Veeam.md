```yaml
sources:
  ProgramData:  
    type: file  
    include:  
      - \ProgramData\Veeam\Backup\* 
    sink: veeam 

sinks:
  veeam: 
    type: hec 
    token: **** 
    url: **** 
```
