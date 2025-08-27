The **CrowdStrike Query Language (CQL)** is the syntax that lets you compose queries to retrieve, process, and analyze data in Falcon LogScale.

- [Helpful-CQL-Queries](https://github.com/CrowdStrike/logscale-community-content/tree/main/Queries-Only/Helpful-CQL-Queries)
- [hunting-queries-crowdstrike](https://github.com/GMoral13/hunting-queries-crowdstrike)
- [CQL Hub](https://cql-hub.com/)
- [CrowdStrike RTR Bookmarklet Creator](https://surbo.github.io/RTRCommands/)
- [ThreatHuntingQuery/CrowdStrike-falcon-query-assets-tree-main-Falcon-Event-Search-Threat-Hunting-Queries](https://github.com/ThreatHuntingQuery/CrowdStrike-falcon-query-assets-tree-main-Falcon-Event-Search-Threat-Hunting-Queries)

You can run the following search query in the Falcon platform to identify ingest lag in your environment. This query shows you the ingest lag in minutes for each event, sorted from lowest to highest lag.
```
| select([#repo, #Vendor, #type, @timestamp, @ingesttimestamp])
| ingest_lag_in_mins := ((@ingesttimestamp-@timestamp)/1000)/60
| sort(ingest_lag_in_mins, limit=20000)
```
