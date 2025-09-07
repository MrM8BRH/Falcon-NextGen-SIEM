```yaml
sources: 
  webserverEvents: 
    type: wineventlog 
    sink: IIS 
    channels: 
      # IIS-related events are mostly in 'Application' and 'System' logs, not 'Security' 
      - name: Application 
        onlyEventIDs:  
          - 1000  # Microsoft-Windows-IIS-IISLogging - Log file created 
          - 1001  # Microsoft-Windows-IIS-IISLogging - Log file closed 
          - 1009  # W3SVC - Worker process failure 
      - name: System 
        onlyEventIDs:  
          - 2001  # W3SVC - Web app pool started 
          - 2002  # W3SVC - Web app pool stopped 
          - 2269  # WAS - App pool crash detected 
          - 5002  # WAS - App pool shutdown 
          - 5003  # WAS - App pool shutdown 
          - 5004  # WAS - App pool shutdown 
          - 5005  # WAS - App pool shutdown 
          - 5186  # WAS - Config change detected 
        # Remote Desktop logon events 
      - name: "Microsoft-Windows-TerminalServices-RemoteConnectionManager/Operational" 
        onlyEventIDs: 
          - 1149  # Remote Desktop Services: User authentication succeeded 
    parser: microsoft-winevent 
    transforms: 
      - type: static_fields 
        fields: 
          source: "webserver" 
          environment: "production" 
    language: 1033 
    format: renderFieldsOnly 
  file_log:
    type: file 
    include:  
      - C:\\inetpub\\logs\\LogFiles\\W3SVC1\\* 
      - C:\\inetpub\\logs\\LogFiles\\W3SVC2\\* 
    sink: IIS2 
    parser: syslog 

sinks: 
  IIS: 
    type: hec 
    token: *** 
    url: *** 
  IIS2: 
    type: hec 
    token: *** 
    url: *** 
```
