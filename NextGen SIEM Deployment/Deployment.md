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
### Access
#### Web Portals
```
https://falcon.crowdstrike.com/login/            # (1)
https://falcon.us-2.crowdstrike.com/login        # (2)
https://falcon.eu-1.crowdstrike.com/login        # (3)
```
#### Firewall Rules
```
[ NextGen SIEM ]
flc-api.crowdstrike.com
flc-api.us-2.crowdstrike.com
flc-api.eu-1.crowdstrike.com

ingest.us-1.crowdstrike.com
ingest.us-2.crowdstrike.com
ingest.eu-1.crowdstrike.com

ingest.logscale.us-1.crowdstrike.com
ingest.logscale.us-2.crowdstrike.com
ingest.logscale.eu-1.crowdstrike.com

[ API ]
api.crowdstrike.com
api.us-2.crowdstrike.com
```

---

#### Deployment

```
Next-Gen SIEM → Log management → Data Onboarding (Data connections → Add connection)

Choose <Source> 

Configure: (Connector name, Parsers (Default Parser, HEC))

Generate API Key: (API Key, API URL)
```
