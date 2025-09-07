```yaml
sources: 
  fileserverEvents: 
    type: wineventlog 
    sink: fileserver 
    channels: 
      # Collect key Fileserver-related events from the Security log 
      - name: Security 
        onlyEventIDs:  
          - 4663   # An attempt was made to access an object (most important for file/folder access) 
          - 4656   # A handle to an object was requested (access intent) 
          - 4658   # The handle to an object was closed (end of access 
          - 4660   # An object was deleted 
          - 4670   # Permissions on an object were changed 
          - 5145   # A network share object was accessed (useful for SMB/file share monitoring) 
    parser: microsoft-winevent 
    transforms: 
      - type: static_fields 
        fields: 
          source: "fileserverEvents" 
          environment: "production" 
    language: 1033 
    format: renderFieldsOnly 
 
sinks: 
  fileserver: 
    type: hec 
    token: *** 
    url: ***
```
