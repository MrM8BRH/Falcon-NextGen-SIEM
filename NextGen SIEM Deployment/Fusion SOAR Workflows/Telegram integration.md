This document outlines the configuration steps required to send CrowdStrike detections to Telegram using **Telegram SOAR Actions** and **Fusion SOAR workflows**.

#### 1. Deploy Telegram SOAR Actions

Navigate to the **CrowdStrike Store**.

1. Open **CrowdStrike Store**
2. Go to **All Apps**
3. Search for **Telegram**
4. Select **Telegram SOAR Actions**
5. Click **Configure**


#### 2. Add Telegram Configuration
Create a new configuration.
```
Configuration name: <name>
Bot Token: <botXXXXXXXXXX:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX>
```

#### 3. Create Fusion SOAR Workflow

Navigate to:

Fusion SOAR → Workflows → Create Workflow

> Create a workflow similar to the following example:
<img width="978" height="769" alt="image" src="https://github.com/user-attachments/assets/ba4cee9f-c4dd-42b4-aa9e-6d56dc50bfe5" />

The workflow should trigger on detections and send the formatted message to the configured Telegram chat.
```
# Telegram "Send Message" Configuration
API Credentials: <from previous configration>
Chat ID: -XXXXXXXXXXXXX
Parse Mode: HTML
```

**Create variables**
```
- Name: CID_Name (Type:String)
- Name: Local_Time (Type:String)
- Name: Severity_Color (Type:String)
```

**Update Variables**
```
- Variable: CID_Name
- Value: ${data['Trigger.CID'].replace("CID", "Name")}

- Variable: Local_Time
- Value: ${cs.timestamp.format(cs.timestamp.parse(data['Trigger.ObservedTime'], 'RFC3339'),'DateTime', 'Asia/Hebron')}

- Variable: Severity_Color
- Value: ${data['Trigger.Detection.SeverityDisplayName'].replace("Informational","🔵🔵🔵🔵🔵").replace("Low","🟢🟢🟢🟢🟢").replace("Medium","🟡🟡🟡🟡🟡").replace("High","🟠🟠🟠🟠🟠").replace("Critical","🔴🔴🔴🔴🔴")}
```

#### 4. Detection Message Templates

4.1 EPP Detection Message
```
${data['WorkflowCustomVariable.Severity_Color']}  ${data['Trigger.Detection.SeverityDisplayName']}  ${data['WorkflowCustomVariable.Severity_Color']} &#10
───────────────────────────────── &#10
🏛️ ${data['WorkflowCustomVariable.CID_Name']} &#10
🛡 Product: EPP Detection &#10 
───────────────────────────────── &#10 
🔎 Detection Name: ${data['Trigger.Detection.Name']} &#10
🔎 Detection Type: ${data['Trigger.Detection.EPP.EPPDetectionType']} &#10
🕒 Detection Time: ${data['WorkflowCustomVariable.Local_Time']} &#10
🚨 IOA Name: ${data['Trigger.Detection.EPP.Behavior.IOAName']} &#10
🧾 IOC Type: ${data['Trigger.Detection.EPP.Behavior.IOCType']} &#10
🌐 IOC Value: ${data['Trigger.Detection.EPP.Behavior.IOCValue']} &#10
🕵️‍♂️ Tactic & Technique: ${data['Trigger.Detection.EPP.Behavior.TacticName']} via ${data['Trigger.Detection.EPP.Behavior.TechniqueName']} &#10
👤 User: ${data['Trigger.Detection.EPP.Process.UserName']} &#10
🏷 Hostname: ${data['Trigger.Detection.EPP.Sensor.Hostname']} &#10
📄 Image: ${data['Trigger.Detection.EPP.Process.ImageFileName']} &#10
🧩 Product Type: ${data['Trigger.Detection.EPP.Sensor.ProductType']} &#10
🌍 Local IP: ${data['Trigger.Detection.EPP.Sensor.LocalIP']} &#10
🔗 Finding URL: <a href="${data['Trigger.SourceEventURL']}">Link</a>
```
4.2 NG-SIEM Detection Message
```
${data['WorkflowCustomVariable.Severity_Color']}  ${data['Trigger.Detection.SeverityDisplayName']}  ${data['WorkflowCustomVariable.Severity_Color']} &#10
───────────────────────────────── &#10
🏛️ ${data['WorkflowCustomVariable.CID_Name']} &#10
🛡 Product: NG-SIEM Detection &#10 
───────────────────────────────── &#10
🔎 Detection Name: ${data['Trigger.Detection.Name']} &#10
🔎 Detection Type: ${data['Trigger.Detection.NGSIEM.DetectionType']} &#10
⚠️ Severity: ${data['Trigger.Detection.SeverityDisplayName']} &#10
🕒 Detection Time: ${data['WorkflowCustomVariable.Local_Time']} &#10
🖥 Hostnames: ${data['Trigger.Detection.NGSIEM.HostNames']} &#10
👤 Users: ${data['Trigger.Detection.NGSIEM.UserNames']} &#10
🧰 Source Product: ${data['Trigger.Detection.NGSIEM.SourceProducts']} &#10
📡 Source Hosts: ${data['Trigger.Detection.NGSIEM.SourceHosts']} &#10
🎯 Destination Hosts: ${data['Trigger.Detection.NGSIEM.DestinationHosts']} &#10
🌐 Source IPs: ${data['Trigger.Detection.NGSIEM.SourceIPs']} &#10
📍 Destination IPs: ${data['Trigger.Detection.NGSIEM.DestinationIPs']} &#10
🔗 Finding URL: <a href="${data['Trigger.SourceEventURL']}">Link</a>
```
4.3 NG-SIEM ThirdParty Message
```
${data['WorkflowCustomVariable.Severity_Color']}  ${data['Trigger.Detection.SeverityDisplayName']}  ${data['WorkflowCustomVariable.Severity_Color']} &#10
───────────────────────────────── &#10
🏛️ ${data['WorkflowCustomVariable.CID_Name']} &#10
🛡 Product: NG-SIEM ThirdParty &#10
───────────────────────────────── &#10
🔎 Detection Name: ${data['Trigger.Detection.Name']} &#10
🔎 Detection Type: ${data['Trigger.Detection.ThirdParty.DetectionType']} &#10
⚠️ Severity: ${data['Trigger.Detection.SeverityDisplayName']} &#10
🕒 Detection Time: ${data['WorkflowCustomVariable.Local_Time']} &#10
🖥 Hostnames: ${data['Trigger.Detection.ThirdParty.HostNames']} &#10
👤 Users: ${data['Trigger.Detection.ThirdParty.UserNames']} &#10
🧰 Source Product: ${data['Trigger.Detection.ThirdParty.SourceProducts']} &#10
📡 Source Hosts: ${data['Trigger.Detection.ThirdParty.SourceHosts']} &#10
🌐 Source IPs: ${data['Trigger.Detection.ThirdParty.SourceIPs']} &#10
🎯 Destination Hosts: ${data['Trigger.Detection.ThirdParty.DestinationHosts']} &#10
📍 Destination IPs: ${data['Trigger.Detection.ThirdParty.DestinationIPs']} &#10
🔗 Finding URL: <a href="${data['Trigger.SourceEventURL']}">Link</a>
```

#### Notes
- The **Bot Token** is obtained from **Telegram BotFather**.
- The **Chat ID** can be a private chat, group, or channel ID.
- Ensure the bot is added to the Telegram group/channel if used.
