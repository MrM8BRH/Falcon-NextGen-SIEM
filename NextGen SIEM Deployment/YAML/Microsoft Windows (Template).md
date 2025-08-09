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
