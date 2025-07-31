event.category[0]

The accepted values:
- api
- authentication
- configuration
- database
- driver
- email
- file
- host
- iam
- intrusion_detection
- library
- malware
- network
- package
- process
- registry
- session
- threat
- vulnerability
- web

	
event.type[0]

The accepted values:
- access
- admin
- allowed
- change
- connection
- creation
- deletion
- denied
- end
- error
- group
- indicator
- info
- installation
- protocol
- start
- user

```YAML
// #region METADATA
/************************************************************
****** Static Metadata Definitions
************************************************************/
| Parser.version := "2.1.3"
| event.module := "CSV"
| ecs.version := "9.0.0"
| Cps.version := "1.0.0"
| Vendor := "CSV"
| event.dataset := "CSV"
| event.kind := "event"
| event.outcome := "success"
| observer.type := "CVS"
| event.category[0] := "configuration"
| event.type[0] := "change"

// #endregion

| kvParse()
| parseCsv(columns=[Timestamp,Action,"Actor UUID","Actor Username","Acting On Behalf Of User UUID","Acting On Behalf Of Username","Group UUID","Group Name","Member UUIDs","Member Names"])
```
