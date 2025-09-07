```yaml
sources: 
  PrintServer_events: 
    type: wineventlog 
    channels: 
      - name: Microsoft-Windows-PrintService/Admin 
      - name: Microsoft-Windows-PrintService/Operational 
    sink: PS
  PrintServer_File: 
   type: file 
   include:  
    - \PaperCut Print Logger\logs\csv\daily\** 
    - \PaperCut Print Logger\logs\csv\monthly\** 
   sink: PS-File 
   parser: Print_csv 

sinks: 
  PS: 
    type: hec 
    token: ***** 
    url: ***** 
 
  PS-File: 
    type: hec 
    token: ***** 
    url: ***** 
```
