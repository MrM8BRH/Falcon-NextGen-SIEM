- [Parse Data - Create a Parser](https://library.humio.com/data-analysis/parsers-create.html)
- [Parse Data - Removing Fields](https://library.humio.com/data-analysis/parsers-remove-field.html)
- [Parse Data - Parsing Timestamps](https://library.humio.com/data-analysis/parsers-parsing-timestamps.html)
---
- [Query Functions - parseJson()](https://library.humio.com/data-analysis/functions-parsejson.html)
- [Query Functions - parseXml()](https://library.humio.com/data-analysis/functions-parsexml.html)
- [Query Functions - parseUrl()](https://library.humio.com/data-analysis/functions-parseurl.html)
- [Query Functions - parseTimestamp()](https://library.humio.com/data-analysis/functions-parsetimestamp.html)
- [Query Functions - findTimestamp()](https://library.humio.com/data-analysis/functions-findtimestamp.html)
- [Query Functions - regex()](https://library.humio.com/data-analysis/functions-regex.html)
- [Query Functions - readFile()](https://library.humio.com/data-analysis/functions-readfile.html)
- [Query Functions - replace()](https://library.humio.com/data-analysis/functions-replace.html)
- [Query Functions - rename()](https://library.humio.com/data-analysis/functions-rename.html)

### Accepted Values for Event Fields

### event.kind
| Value          | Description                          |
|----------------|--------------------------------------|
| `alert`        | Security or operational alert        |
| `asset`        | Asset-related information            |
| `enrichment`   | Data enrichment event                |
| `event`        | Generic event                        |
| `metric`       | Measured performance/value          |
| `state`        | State change event                   |
| `pipeline_error`| Data processing pipeline failure     |
| `signal`       | Detection signal                     |

---

### event.outcome
| Value       | Description                         |
|-------------|-------------------------------------|
| `failure`   | Operation failed                    |
| `success`   | Operation succeeded                 |
| `unknown`   | Outcome could not be determined     |

---

### event.category[0]
| Value                   | Domain                        |
|-------------------------|-------------------------------|
| `api`                   | API operations                |
| `authentication`        | Login/logout activities       |
| `configuration`         | System configuration changes  |
| `database`              | Database operations           |
| `driver`                | Device driver events          |
| `email`                 | Email activities              |
| `file`                  | File system operations        |
| `host`                  | Host-level events             |
| `iam`                   | Identity & Access Management  |
| `intrusion_detection`   | IDS/IPS alerts                |
| `library`               | Software library events       |
| `malware`               | Malware detections            |
| `network`               | Network connections           |
| `package`               | Software package operations   |
| `process`               | Process execution             |
| `registry`              | System registry changes       |
| `session`               | User session activity         |
| `threat`                | Threat intelligence events    |
| `vulnerability`         | Vulnerability scans           |
| `web`                   | Web server activities         |

---

### event.type[0]
| Value           | Trigger Context                 |
|-----------------|---------------------------------|
| `access`        | Resource access attempt         |
| `admin`         | Administrative action           |
| `allowed`       | Permitted operation             |
| `change`        | Modification event              |
| `connection`    | Network connection              |
| `creation`      | Resource creation               |
| `deletion`      | Resource removal                |
| `denied`        | Blocked operation               |
| `end`           | Session/process termination     |
| `error`         | Error condition                 |
| `group`         | Group operations                |
| `indicator`     | Security indicator              |
| `info`          | Informational event             |
| `installation`  | Software installation           |
| `protocol`      | Protocol-specific event         |
| `start`         | Session/process initiation      |
| `user`          | User-specific action            |
