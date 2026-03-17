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
<img width="1349" height="788" alt="image" src="https://github.com/user-attachments/assets/f58c2b9a-f394-49fd-afb6-2be8d17e2473" />

The workflow should trigger on detections and send the formatted message to the configured Telegram chat.
```
# Telegram "Send Message" Configuration
API Credentials: <from previous configration>
Chat ID: -XXXXXXXXXXXXX 
```

#### 4. Detection Message Templates

4.1 EPP Detection Message
```
🛡 Product: EPP Detection 🔎 Detection Name: ${data['Trigger.Detection.Name']} 🔎 Detection Type: ${data['Trigger.Detection.EPP.EPPDetectionType']} ⚠️ Severity: ${data['Trigger.Detection.SeverityDisplayName']} 🕒 Detection Time: ${data['Trigger.ObservedTime']} 🚨 IOA Name: ${data['Trigger.Detection.EPP.Behavior.IOAName']} 🧾 IOC Type: ${data['Trigger.Detection.EPP.Behavior.IOCType']} 🌐 IOC Value: ${data['Trigger.Detection.EPP.Behavior.IOCValue']} 👤 User: ${data['Trigger.Detection.EPP.Process.UserName']} 📄 Image: ${data['Trigger.Detection.EPP.Process.ImageFileName']} 🏷 Hostname: ${data['Trigger.Detection.EPP.Sensor.Hostname']} 🧩 Product Type: ${data['Trigger.Detection.EPP.Sensor.ProductType']} 🌍 Local IP: ${data['Trigger.Detection.EPP.Sensor.LocalIP']} 🔗 Link: ${data['Trigger.SourceEventURL']}
```
4.2 NG-SIEM Detection Message
```
🛡 Product: NG-SIEM Detection 🔎 Detection Name: ${data['Trigger.Detection.Name']} 🔎 Detection Type: ${data['Trigger.Detection.NGSIEM.DetectionType']} ⚠️ Severity: ${data['Trigger.Detection.SeverityDisplayName']} 🕒 Detection Time: ${data['Trigger.ObservedTime']} 🖥 Hostnames: ${data['Trigger.Detection.NGSIEM.HostNames']} 👤 Users: ${data['Trigger.Detection.NGSIEM.UserNames']} 🧰 Source Product: ${data['Trigger.Detection.NGSIEM.SourceProducts']} 📡 Source Hosts: ${data['Trigger.Detection.NGSIEM.SourceHosts']} 🎯 Destination Hosts: ${data['Trigger.Detection.NGSIEM.DestinationHosts']} 🌐 Source IPs: ${data['Trigger.Detection.NGSIEM.SourceIPs']} 📍 Destination IPs: ${data['Trigger.Detection.NGSIEM.DestinationIPs']} 🔗 Link: ${data['Trigger.SourceEventURL']}
```
4.3 NG-SIEM ThirdParty Message
```
🛡 Product: NG-SIEM ThirdParty 🔎 Detection Name: ${data['Trigger.Detection.Name']} 🔎 Detection Type: ${data['Trigger.Detection.ThirdParty.DetectionType']} ⚠️ Severity: ${data['Trigger.Detection.SeverityDisplayName']} 🕒 Detection Time: ${data['Trigger.ObservedTime']} 🖥 Hostnames: ${data['Trigger.Detection.ThirdParty.HostNames']} 👤 Users: ${data['Trigger.Detection.ThirdParty.UserNames']} 🧰 Source Product: ${data['Trigger.Detection.ThirdParty.SourceProducts']} 📡 Source Hosts: ${data['Trigger.Detection.ThirdParty.SourceHosts']} 🌐 Source IPs: ${data['Trigger.Detection.ThirdParty.SourceIPs']} 🎯 Destination Hosts: ${data['Trigger.Detection.ThirdParty.DestinationHosts']} 📍 Destination IPs: ${data['Trigger.Detection.ThirdParty.DestinationIPs']} 🔗 Link: ${data['Trigger.SourceEventURL']}
```

#### Notes
- The **Bot Token** is obtained from **Telegram BotFather**.
- The **Chat ID** can be a private chat, group, or channel ID.
- Ensure the bot is added to the Telegram group/channel if used.
