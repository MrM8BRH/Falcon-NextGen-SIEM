```yaml
sources: 
  sqlServerEvents: 
    type: wineventlog 
    sink: SQL 
    channels: 
      - name: Application 
        onlyEventIDs: 
          - 17137  # SQL Server started 
          - 17138  # SQL Server stopped 
          - 18456  # Login failed 
          - 26010  # Login succeeded 
          - 26011  # Login failed (alternative) 
          - 17055  # General SQL errors 
          - 18264  # Database backup 
          - 18265  # Log backup 
    parser: microsoft-winevent 
    transforms: 
      - type: static_fields 
        fields: 
          source: "sql_server" 
          environment: "production" 
    language: 1033 
    format: renderFieldsOnly 
 
sinks: 
  SQL: 
    type: hec 
    token: *** 
    url: ****
```
