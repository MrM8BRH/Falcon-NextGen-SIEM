```yaml
sources: 
  brokerEvents: 
    type: wineventlog 
    sink: broker 
    channels: 
      # Terminal Services - Session Broker (Client) 
      - name: "Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational" 
      # Terminal Services - Session Broker (Operational) 
      - name: "Microsoft-Windows-TerminalServices-SessionBroker/Operational" 
      # Terminal Services - Local Session Manager (Logon, logoff, disconnect, reconnect) 
      - name: "Microsoft-Windows-TerminalServices-LocalSessionManager/Operational" 
      # Terminal Services - Remote Connection Manager (broker connections) 
      - name: "Microsoft-Windows-TerminalServices-RemoteConnectionManager/Operational" 
    parser: microsoft-winevent 
    transforms: 
      - type: static_fields 
        fields: 
          source: "broker" 
          environment: "production" 
    language: 1033 
    format: renderFieldsOnly 
 
sinks: 
  broker: 
    type: hec 
    token: **** 
    url: ****
```
