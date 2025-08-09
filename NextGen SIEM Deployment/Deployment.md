### Key Pages & Locations (Navigation Guide)

#### Permissions and Roles
- **Path**: `Host setup and management → Falcon users → User management`

#### Rules (Next-Gen SIEM)
- **Path**: `Next-Gen SIEM → Monitor and Investigate → Rules`

#### Dashboards (Next-Gen SIEM)
- **Path**: `Next-Gen SIEM → Log management → Dashboards`

#### Integrations (Next-Gen SIEM)
- **Path**: `Next-Gen SIEM → Log management →` 
  - Data onboarding
  - Lookup files
  - Data settings

#### Download FLC (Falcon LogScale Collector)
- **Path 1**: `Menu → Next-Gen SIEM → Data onboarding → Fleet management → Fleet overview → Get LogScale Collector`
- **Path 2**: `Menu → Support and resources → Tools downloads → LogScale Collector`

#### Open Support Ticket
- **Path**: `Support and resources → Support portal`

---

#### Deployment

```
Next-Gen SIEM → Log management → Data Onboarding (Data connections → Add connection)

Choose <Source> 

Configure: (Connector name, Parsers (Default Parser, HEC))

Generate API Key: (API Key, API URL)
```

#### Syslog
<details>
  <summary>Sample configuration file (YAML)</summary>

```YAML
#####
## Sample configuration file for Linux or Microsoft Windows.
## This is YAML, so structure and indentation is important.
## Lines can be uncommented by removing the #. You should not need to change the number of spaces after that.
## Config options have a single #, comments have a ##. Only uncomment the single # lines if you need them.
#####
 
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
    url: https://XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX.ingest.YY-Y.crowdstrike.com
    proxy: none
    workers: 4
    ## This sets the maximum single event size to. You can change as needed. the maximum value is 950000.
    maxEventSize: 910000
    ## This sets the maximum payload size for a single batch of events. The max value is maxBatchSize: 12000000.
    maxBatchSize: 12000000

```
</details>


#### Windows
<details>
<summary>Sample configuration file for Microsoft Windows</summary>

```YAML
#####
## Sample configuration file for Microsoft Windows.
## This is YAML, so structure and indentation is important.
## Lines can be uncommented by removing the #. You should not need to change the number of spaces after that.
## Config options have a single #, comments have a ##. Only uncomment the single # lines if you need them.
#####

## Uncomment dataDirectory if you need to manually set the directory.
## Note: Not used with Data Ingest / Fleet configuration
#dataDirectory: C:\ProgramData\LogScale Collector
 
sources:
  ## Collect windows event logs
  windows_events:
    type: wineventlog
    ## Add other channels by simple adding additional "name" lines.
    ## The following command can be used to find other channels:
    ## Get-WinEvent -ListLog * -EA silentlycontinue | sort-object -Property Recordcount -desc
    channels:
      - name: Application
        #excludeEventIDs: [903, 900]
      - name: Security
        #onlyEventIDs: [4624,4634,4672]
      - name: System
      - name: Windows PowerShell
      - name: ForwardedEvents
    ## Set language to en-US
    #language: 1033
    ## Don't send the raw XML
    #includeXML: false
    sink: next-gen-siem-windows

  fileRead:
    type: file
    include:
    - c:\temp\flc_files\csv_sample2.csv
    exclude: 
    - c:\temp\flc_files\ignore.txt
    ## Optional: Exclude files with specific extensions
    #excludeExtensions:
    #   - "gz"
    #   - "zip"
    ## Configure multiline parsing if needed
    #multiLineBeginsWith: '^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}'
    sink: next-gen-siem-fileread
    
## Each sink will have a unique token and url that correspond to Data Connector you wish to send data to.     
sinks:

  ## This sink matches to the configuration in the "Windows" type sources above  
  next-gen-siem-windows:
    type: hec
    ## Replace with your specified ingest token.
    token: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    ## Replace with the "Ingest URL" on the FLC download page. It must include the "https://" at the beginning.
    ## NOTE: you MUST REMOVE the "services/collector" from the URL if it exists.  
    url: https://XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX.ingest.YY-Y.crowdstrike.com
    ## Keep this option as "none" unless you actually need a proxy.
    proxy: none  
    ## This sets the maximum single event size to 900KB. You can change as needed.
    maxEventSize: 910000
    ## This sets the maximum payload size (bytes) for a single batch of events. The max value is maxBatchSize: 12000000.
    maxBatchSize: 12000000
    ## Uncomment if you would like to force a specific level of gzip compression. 9 is the highest.
    #compression: gzip
    #compressionLevel: 9
    ## Uncomment if you want to use disk for event queue storage instead of memory.
    ## Please note this may be slower than a memory queue.
    #queue:
      #type: disk
      #fullAction: pause
      #maxLimitInMB: 4096
    ## Add more workers to processing. The default is 4 workers. 
    ## This can be increased if FLC is falling behind on processing.
    workers: 4
  
  ## This sink matches to the configuration in the "File" type sources above  
  next-gen-siem-fileread:
    type: hec
    ## Replace with your specified ingest token.
    token: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    ## Replace with the "Ingest URL" on the FLC download page. It must include the "https://" at the beginning.
    ## NOTE: you MUST REMOVE the "services/collector" from the URL if it exists.  
    url: https://XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX.ingest.YY-Y.crowdstrike.com
    ## Keep this option as "none" unless you actually need a proxy.
    proxy: none  
    ## This sets the maximum single event size to 900KB. You can change as needed.
    maxEventSize: 910000
    ## This sets the maximum payload size (bytes) for a single batch of events. The max value is maxBatchSize: 12000000.
    maxBatchSize: 12000000
    ## Uncomment if you would like to force a specific level of gzip compression. 9 is the highest.
    #compression: gzip
    #compressionLevel: 9
    ## The queue size is reduced to 64 MB because the input is read from persistent files.
    queue:
      type: memory  
      maxLimitInMB: 64
    ## Add more workers to processing. The default is 4 workers. 
    ## This can be increased if FLC is falling behind on processing.
    workers: 4
```
</details>

<details>
<summary>Custom configuration file for Microsoft Windows</summary>
  
```YAML
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
          - 4647   # User initiated logoff
          - 4648   # Logon attempt using explicit credentials (network logon)
          - 4672   # Special privileges assigned to new logon (e.g., Administrator)
          - 4688   # A new process has been created
          - 4689   # A process has exited
          - 4720   # User account created
          - 4722   # User account enabled
          - 4723   # An attempt was made to change an account's password
          - 4724   # An attempt was made to reset an account's password
          - 4725   # User account disabled
          - 4726   # User account deleted
          - 4727   # Security-enabled global group created
          - 4728   # Member added to a security-enabled global group
          - 4729   # Member removed from a security-enabled global group
          - 4735   # Security-enabled local group changed
          - 4737   # Security-enabled global group changed
          - 4738   # User account changed
          - 4740   # Account locked out
          - 4761   # A trust relationship was created or changed
          - 4762   # A trust relationship was removed
          - 4767   # User account unlocked
          - 4776   # Credential validation (used by domain controller)
          - 4780   # Account name enumerated
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
</details>
