 The Falcon LogScale Collector is the native log shipper for LogScale. It can collect and send events to a LogScale repository, using LogScale ingest tokens to route data to the relevant repositories.

Falcon LogScale Collector, available on Linux, macOS and Windows can be managed centrally through Fleet Management, enabling you to centrally manage multiple instances of Falcon LogScale Collector from within LogScale.

Falcon LogScale Collector can collect data from several [sources](https://library.humio.com/falcon-logscale-collector/log-collector-config-common-sources.html):
- Command sources
- Windows events
- Files
- Linux systems
- syslog
- unifiedlog
- JournalD sources

Links:
- [Download and Install Falcon LogScale Collector Using Curl Commands (Full Install)](https://library.humio.com/falcon-logscale-collector/log-collector-install-full.html)
- [Install Falcon LogScale Collector on Linux - Custom](https://library.humio.com/falcon-logscale-collector/log-collector-install-custom-linux.html)
- [Install Falcon LogScale Collector on Windows - Custom](https://library.humio.com/falcon-logscale-collector/log-collector-install-custom-windows.html)

Linux Servers
```
# should apply to read file on Linux
sudo usermod -a -G adm humio-log-collector
```
Windows Servers
```
# Service
Run the FLC (ensure it's set as an automatic service on the Windows server)
```
Configuration File Paths:
- Linux (full) → `/etc/logscale-log-collector/config.yaml`
- Linux (Custom) → `/etc/humio-log-collector/config.yaml`
- Windows(Full) → `C:\\Program Files (x86)\\CrowdStrike\\Logscale Log Collector\\config.yaml`
- Windows(Custom) → `C:\\Program Files (x86)\\CrowdStrike\\Humio Log Collector\\config.yaml`
- MacOS → `/usr/local/etc/logscale-collector/config.yaml `

[Data Directory](https://library.humio.com/falcon-logscale-collector/log-collector-config-common-data.html)

The top level element defines the where the Falcon LogScale Collector will create its database.db (database) file.

By default the following paths are used:
- Linux → `/var/lib/logscale-collector`
- Windows → `C:\ProgramData\Log Collector`
- macOS → `/var/local/logscale-collector `

<details>
 <summary>YAML</summary>

```YAML
dataDirectory: data
flags: ...
sources:
  apache_logs: ...

sinks:
  my_humio_instance: ...
```
</details>

```diff
- Note : This section is not applicable if you are using remote configuration management and must not be included.
```

[Sinks](https://library.humio.com/falcon-logscale-collector/log-collector-config-common-sinks.html)

The sinks block configures the sinks (where the data will be sent) that are used by the source or sources. 

<details>
 <summary>YAML</summary>

```YAML
# Define the sink (destination) for the logs
sinks:
  example_sink:
    type: logscale
    url: "https://cloud.humio.com/"  # Replace with your LogScale instance URL
    token: "${INGEST_TOKEN}"  # Use environment variable for the ingest token

    # Optional: Override the maximum batch size (in bytes)
    # maxBatchSize: 8388608  # 8 MB
    # Default is 16 MB. It should not be increased, but may be lowered if needed.

    # Optional: Override the maximum event size (in bytes)
    # maxEventSize: 1048576  # 1 MB
    # Default is 1 MB, which is the default maximum that LogScale supports.

    # Optional: Specify compression method
    # compression: "gzip"  # Options: "auto", "none", "gzip", "deflate"
    # Default is "auto", which attempts "gzip" but falls back to "none" if unsupported.

    # Optional: Specify compression level (0-9, where 0 is no compression and 9 is best compression)
    # compressionLevel: 1

    # Optional: Specify a proxy
    # proxy: "http://proxy.example.com:8080"
    # Default is "auto", which uses the system proxy if possible, or falls back to "none".
    # You can also use "system" to force system proxy use, or "none" to disable proxy use.

    # Optional: Configure TLS options
    # tls:
    #   insecure: false  # Set to true to disable certificate validation (not recommended)
    #   # Note: caCert and caFile are mutually exclusive. Use only one of them.
    #   caCert: "-----BEGIN CERTIFICATE-----\n...\n-----END CERTIFICATE-----"
    #   # caCert is for providing an inline PEM encoded CA certificate
    #   # caFile: "/path/to/ca/cert.pem"
    #   # caFile is for providing a path to a file containing a PEM encoded CA certificate

    # Optional: Number of worker threads for sending data
    # workers: 4

    # Configure the queue for buffering events
    queue:
      # Memory queue configuration
      type: memory
      maxLimitInMB: 1024  # Maximum queue size in MB
      # Note: The queue size can be lowered if needed, but it should not be necessary to increase it.
      # Optional: Action to take when queue is full
      # fullAction: "pause"  # Options: "pause" or "deleteOldest"
      # Optional: Flush timeout in milliseconds
      # flushTimeOutInMillisecond: 1000

    # Alternate disk queue configuration (uncomment to use)
    # queue:
    #   type: disk
    #   maxLimitInMB: 4096  # Maximum queue size in MB
    #   # Note: Changing the disk queue size requires a rewrite of the queue storage file.
    #   # Optional: Action to take when queue is full
    #   # fullAction: "pause"  # Options: "pause" or "deleteOldest"
    #   # Optional: Storage directory
    #   # storageDir: "/path/to/queue/storage"
    #   # Default storage directory is under the dataDirectory of the program.
  my_examplesink1:
    type: logscale
    url: "https://cloud.humio.com/"  # Replace with your LogScale instance URL
    token: "${INGEST_TOKEN}"  # Use environment variable for the ingest token
# Define the sources for data collection
sources:
  # Add your sources here. Examples include:
  # - file
  # - syslog
  # - wineventlog
  # - journald
  # - cmd
  # - unifiedlog
```
</details>

```diff
- Note : When syslog is the data source, only one sink can be configured per syslog data source. 
```

