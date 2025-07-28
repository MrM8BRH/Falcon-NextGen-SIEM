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

Data Directory

The top level element defines the where the Falcon LogScale Collector will create its database.db (database) file.

By default the following paths are used:
- Linux → `/var/lib/logscale-collector`
- Windows → `C:\ProgramData\Log Collector`
- macOS → `/var/local/logscale-collector `
```diff
- Note :  This section is not applicable if you are using remote configuration management and must not be included.
```
