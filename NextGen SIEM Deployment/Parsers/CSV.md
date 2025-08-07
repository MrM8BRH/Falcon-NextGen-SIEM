- [Query Functions - parseCsv()](https://library.humio.com/data-analysis/functions-parsecsv.html)
- [Query Functions - kvParse()](https://library.humio.com/data-analysis/functions-kvparse.html)


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
| event.category[0] := "file"
| event.type[0] := "user"

// #endregion

| parseCsv(columns=[Timestamp,Action,"Actor UUID","Actor Username","Acting On Behalf Of User UUID","Acting On Behalf Of Username","Group UUID","Group Name","Member UUIDs","Member Names"])
```

