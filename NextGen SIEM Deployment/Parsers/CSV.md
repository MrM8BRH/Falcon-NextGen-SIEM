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

| kvParse()
| parseCsv(columns=[Timestamp,Action,"Actor UUID","Actor Username","Acting On Behalf Of User UUID","Acting On Behalf Of Username","Group UUID","Group Name","Member UUIDs","Member Names"])
```


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
