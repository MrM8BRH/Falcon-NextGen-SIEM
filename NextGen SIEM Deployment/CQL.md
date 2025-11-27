The **CrowdStrike Query Language (CQL)** is the syntax that lets you compose queries to retrieve, process, and analyze data in Falcon LogScale.

- [LogScale Documentation - Query Functions](https://library.humio.com/data-analysis/functions.html)
- [CQL Hub](https://cql-hub.com/)
- [Prediciv | Query Lab](https://querylab.prediciv.com/)
- [Splunk (SPL) to LogScale/NG-SIEM (CQL) Query Converter](https://dataelicit.com/spl-to-cql-converter)
- [Helpful-CQL-Queries (logscale-community-content)](https://github.com/CrowdStrike/logscale-community-content/tree/main/Queries-Only/Helpful-CQL-Queries)
- [hunting-queries-crowdstrike](https://github.com/GMoral13/hunting-queries-crowdstrike)
- [CrowdStrike RTR Bookmarklet Creator](https://surbo.github.io/RTRCommands/)
- [ThreatHuntingQuery/CrowdStrike-falcon-query-assets-tree-main-Falcon-Event-Search-Threat-Hunting-Queries](https://github.com/ThreatHuntingQuery/CrowdStrike-falcon-query-assets-tree-main-Falcon-Event-Search-Threat-Hunting-Queries)

You can run the following search query in the Falcon platform to identify ingest lag in your environment. This query shows you the ingest lag in minutes for each event, sorted from lowest to highest lag.
```
| select([#repo, #Vendor, #type, @timestamp, @ingesttimestamp])
| ingest_lag_in_mins := ((@ingesttimestamp-@timestamp)/1000)/60
| sort(ingest_lag_in_mins, limit=20000)
```

### CrowdStrike Query Language (CQL) Examples

#### 🍯 Quick Reference Table

| Category                     | Function / Command | Description                                    |
| ---------------------------- | ------------------ | ---------------------------------------------- |
| **Rename & Transform**       | `rename()`         | Rename one or multiple fields                  |
|                              | `replace()`        | Replace a substring or word                    |
| **Time & Date**              | `formatTime()`     | Convert timestamps to readable format          |
|                              | `time:hour()`      | Extract the hour from a timestamp              |
| **Statistics & Aggregation** | `stats(count())`   | Count total events                             |
|                              | `groupBy()`        | Group and deduplicate data                     |
|                              | `eval()`           | Create or calculate new fields                 |
|                              | `max()` / `min()`  | Find maximum or minimum values                 |
|                              | `top()`            | Get top N values (e.g., most viewed URLs)      |
| **Data Export**              | `select()`         | Choose fields for export (e.g., CSV)           |
| **Units & Conversion**       | `unit:convert()`   | Convert file size or time units                |
| **Lookups & IOCs**           | `ioc:lookup()`     | Enrich data with IOC lookups (IP, URL)         |
| **Regex & Parsing**          | `regex()`          | Extract or match data using regex              |
| **Sorting**                  | `sort()`           | Sort results numerically or alphabetically     |
| **Logic & Categorization**   | `if()`, `in()`     | Conditional logic and error grouping           |
| **Email & Joins**            | `selfJoinFilter()` | Filter related email events by sender/receiver |

---

## 🏷️ Rename Fields

**Rename a single field**

```cql
rename(field=badName, as=goodName)
```

**Rename multiple fields to readable names**

```cql
rename(field=[[src_ip, source_address], [dst_ip, destination_address], [src_port, source_port], [dst_port, destination_port]])
```

---

## ⏱️ Time and Date Operations

**Convert Timestamp**

```cql
| formatTime(format="@timestamp", as="yyyy-MM-dd HH:mm:ss", timezone="Asia/Jerusalem")
```

**Summarize events per hour**

```cql
hr := time:hour(field="@ingesttimestamp")
| groupBy(hr)
```

---

## 📊 Statistics and Aggregations

**Count total events**

```cql
stats(function=count())
```

**Deduplicate content based on a field**

```cql
groupBy(field, function=tail(1))
```

**Calculate the sum of two fields**

```cql
result := a + b
```

**Create new fields using expressions**

```cql
eval(c = a + b)
```

**Calculate the maximum value**

```cql
max(responsetime)
```

**Calculate the minimum value**

```cql
min(responsetime)
```

---

## 📂 Data Selection and Export

**Select fields to export as CSV**

```cql
select([@timestamp, @rawstring])
```

---

## 🔁 Data Transformation

**Convert file size and transfer time units**

```cql
unit:convert(field=file_size, from="B", to="MB")
| unit:convert(field=transfer_time, from="ms", to="s")
| table([file_size, transfer_time])
```

**Replace a substring**

```cql
replace(regex=propperties, with=properties)
```

---

## 🧬 Lookup and IOC Correlation

**Look up IP IOCs**

```cql
ioc:lookup(field=ip, type=ip_address)
```

**Look up URL IOCs**

```cql
ioc:lookup("url", type="url", confidenceThreshold="low")
```

---

## 🧬 Regex and Pattern Extraction

**Extract integer part of a number**

```cql
regex("(?<b>\\d+)\\..*", field=a)
```

**List the top most viewed pages**

```cql
regex(regex="/.*/(?<url_page>\S+\.page)", field=url)
| top(url_page, limit=12, rest=others)
```

**Regex example**

```cql
regex("expression", flags="")
```

---

## 🔢 Sorting and Ranking

**Sort examples**

```cql
# Example 1
| sort(field=_count, type=number, order=desc, limit=1)

# Example 2
| sort(ref_url, type=string, order=desc, limit=12)

# Example 3
| sort([ref_url, method], type=string, order=[desc, asc], limit=12)
```

**Identify frequently failing URLs**

```cql
statuscode = "404"
| top(url, limit=20)
```

---

## 🚨 Conditional and Categorization Logic

**Categorize errors by severity**

```cql
critical_status := if((in(status, values=["500", "404"])), then="Critical", else="Non-Critical")
```

---

## 📧 Email Data Example

**Retrieve all emails with attachments sent from one person to another**

```cql
selfJoinFilter(field=email_id, where=[{ from=peter }, { to=paul }])
| attachment=*
```
