Pages
```
Permissions and Roles: Host setup and management → Falcon users → User management
Rules: Next-Gen SIEM → Monitor and Investigate → Rules
Dashboards: Next-Gen SIEM → Log management → Dashboards
Integrations: Next-Gen SIEM → Log management → (Data onboarding, Lookup files, Data settings)
Download FLC: Menu → Support and resources → Tools downloads → LogScale Collector
Open Ticket: Support and resources → Support portal
```
Firewall Rules
```
flc-api.us-2.crowdstrike.com
ingest.us-2.crowdstrike.com
```

CrowdStrike Side
```
Next-Gen SIEM → Log management → Data Onboarding (Data connections → Add connection)

Choose <FortiGate> 

Configure: (Connector name, Parsers (Default Parser, HEC))

Generate API Key: (API Key, API URL)
```
FLC Side

- [Download and Install Falcon LogScale Collector Using Curl Commands (Full Install)](https://library.humio.com/falcon-logscale-collector/log-collector-install-full.html)
- [Install Falcon LogScale Collector on Linux - Custom](https://library.humio.com/falcon-logscale-collector/log-collector-install-custom-linux.html)
- [Install Falcon LogScale Collector on Windows - Custom](https://library.humio.com/falcon-logscale-collector/log-collector-install-custom-windows.html)
```
sudo usermod -a -G adm humio-log-collector → should apply to read file on Linux
Run the FLC (set as automatic service)
Edit C:\Program Files (x86)\CrowdStrike\Humio Log Collector\config.yaml as the following format
```
```
dataDirectory: C:\ProgramData\LogScale Collector\

sources:
  syslog_udp_1514:
    type: syslog
    mode: udp
    port: 1514
    sink: next-gen-siem
 
  syslog_tcp_1514:
    type: syslog
    mode: tcp
    port: 1514
    sink: next-gen-siem
     
sinks:
  next-gen-siem:
    type: hec
    token: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    url: https://ingest.us-1.crowdstrike.com/api/ingest/hec/ccb0ffcfe120463a86270238acb372ad/v1
    proxy: none
    workers: 4
    ## This sets the maximum single event size to. You can change as needed. the maximum value is 950000.
    maxEventSize: 910000
    ## This sets the maximum payload size for a single batch of events. The max value is maxBatchSize: 16777216.
    maxBatchSize: 5242880
```
AD Configuration (Fleet Management)
```
sources:
  activeDirectoryEvents:
    type: wineventlog
    sink: AD
    channels:
      # Collect key AD-related events from the Security log
      - name: Security
        onlyEventIDs: 
          - 4624   # Successful logon
          - 4625   # Failed logon
          - 4634   # Logoff
          - 4672   # Special privileges assigned to new logon
          - 4720   # User account created
          - 4722   # User account enabled
          - 4723   # Password change attempt
          - 4724   # Password reset
          - 4725   # User account disabled
          - 4726   # User account deleted
          - 4740   # Account locked out
          - 4647
          - 4648
          - 4776
          - 4727
    parser: microsoft-winevent
    transforms:
      - type: static_fields
        fields:
          source: "active_directory"
          environment: "production"
    language: 1033
    format: renderFieldsOnly

sinks:
  AD:
    type: hec
    token: $INGEST_TOKEN  # Replace with your actual token
    url: $LOGSCALE_URL    # Replace with your ingest URL (must start with https://)
```
```
Restart humio service
```
FortiGate Firewall Side
```
config log syslogd2 setting
 set status enable
 set server "LogScal IP"
 set source-ip "Forti Mgmt IP"
end
```
- [Configuration Elements - Sources](https://library.humio.com/falcon-logscale-collector/log-collector-config-common-sources.html)
- [Query Functions - parseCsv()](https://library.humio.com/data-analysis/functions-parsecsv.html)
- [Query Functions - kvParse()](https://library.humio.com/data-analysis/functions-kvparse.html)
- [Query Functions - parseJson()](https://library.humio.com/data-analysis/functions-parsejson.html)
- [Query Functions - parseXml()](https://library.humio.com/data-analysis/functions-parsexml.html)
- [Query Functions - parseUrl()](https://library.humio.com/data-analysis/functions-parseurl.html)
- [Query Functions - parseTimestamp()](https://library.humio.com/data-analysis/functions-parsetimestamp.html)
- [Query Functions - findTimestamp()](https://library.humio.com/data-analysis/functions-findtimestamp.html)
- [Query Functions - regex()](https://library.humio.com/data-analysis/functions-regex.html)
- [Query Functions - readFile()](https://library.humio.com/data-analysis/functions-readfile.html)
- [Query Functions - replace()](https://library.humio.com/data-analysis/functions-replace.html)
- [Query Functions - rename()](https://library.humio.com/data-analysis/functions-rename.html)
- [Parse Data - Create a Parser](https://library.humio.com/data-analysis/parsers-create.html)
- [Parse Data - Removing Fields](https://library.humio.com/data-analysis/parsers-remove-field.html)
- [Parse Data - Parsing Timestamps](https://library.humio.com/data-analysis/parsers-parsing-timestamps.html)
