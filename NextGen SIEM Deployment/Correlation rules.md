What is a correlation rule?
---------------------------
Correlation rules in the Falcon platform are customizable detection mechanisms that allow you to identify specific patterns of activity within your data. 

These rules run as scheduled searches and generate custom incidents or detections when matches are found.

<div align="center">
<img width="536" height="324" alt="image" src="https://github.com/user-attachments/assets/ba592cb1-91ab-42d4-99ac-f485b768778e" />
</div>

> Just as a detective connects clues to solve a case, correlation rules connect separate security events to tell you the full story of what's happening in your environment.

> They transform individual alerts into meaningful intelligence that helps you spot potential threats faster.

Key components of correlation rules
-----------------------------------
- **Event sources:** The data sources that are monitored to detect and analyze events for correlation purposes.
- **Correlation logic:** The conditions and relationships defined to identify meaningful connections between different events.
- **Time windows:** The specific timeframe within which related events must occur to be considered correlated.
- **Threshold:** The predefined limit or value that must be exceeded to trigger an event or action, based on your organization's risk tolerance.
- **Actions:** The outcomes or responses triggered when the conditions of the correlation rule are met.

[NG-SIEM Rule Templates](https://falcon.crowdstrike.com/documentation/category/a0000000/rule-templates)


**Sensor rules**
- Enabled out of the box for security teams who have deployed the sensor
- Monitors and collects raw events directly from the endpoint
- Operates on first-party (sensor) data only
- Executes in real-time on the local system or near-real time in the cloud
- Focuses on individual behaviors or events

**Correlation rules**
- Enabled by your security team
- Looks for patterns and relationships between different events
- Operates at the cloud level (Falcon platform) with first-party and third-party data
- Processes data after it has been collected and sent to the cloud
- Analyzes multiple events across time and multiple systems

**Key distinction**
Think of sensor rules as the "collectors" that gather individual pieces of evidence, while correlation rules are the "analysts" that piece multiple pieces of evidence together to identify broader patterns or threats.
