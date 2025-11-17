---
title: "Visualizing Logs with Microsoft Sentinel Workbooks"
created: 2025-11-17
modified: 2025-11-17
description: "Power Automate comes with \"When I receive Email\" feature which enables us to perform actions on all new incoming emails."
keywords: ["Microsoft Defender XDR", "Microsoft Sentinel", "Workbooks"]
tags: ["SIEM", "Workbook"]
draft: true
---

## Information

Microsoft Sentinel Workbooks are designed to visualize logs that occurs in our environment so we can better secure and respond to incidents, alerts, and threat actors.

## Implemention

Using Microsoft Sentinel Workbooks we can implement monitoring to highly privileged accounts and other assets which needs more attention than others. Here's a overview of a implementation which monitors failed logon attempts on accounts with domain admin privileges:

![[0000 Visualizing-Logs-with-Microsoft-Sentinel-Workbooks-00.png]]

Using the example above we are able to monitor failed logon attempts to domain admin accounts by the time these events occurred in our environment. We can also create tiles to better highligh the total failed logon attempts that has occurred in our environment. Here's a overview of the KQL to implement the timechart and tiles.

```sql title="Workbook - Timechart for Users"
let domainAdmins = (_GetWatchlist("DomainAdmins") | project UserPrincipalName);
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == "4625"
| where TargetUserName in (domainAdmins)
| summarize arg_max(TimeGenerated, *) by TimeGenerated
| summarize count() by bin(TimeGenerated, 1h), TargetUserName
| render timechart 
```

```sql title="Workbook - Tiles for Users"
let domainAdmins = (_GetWatchlist("DomainAdmins") | project UserPrincipalName);
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == "4625"
| where TargetUserName in (domainAdmins)
| summarize arg_max(TimeGenerated, *) by TimeGenerated
| summarize FailedLogons = count() by TargetUserName
| project TargetUserName, FailedLogons
| sort by FailedLogons
```

Additionally, we can also create another group to better display where these events occurred in our environment. Here's a overview of the implementations:

![[0000 Visualizing-Logs-with-Microsoft-Sentinel-Workbooks-01.png]]

Using the image above that allows us to see which computers has been experiencing a high frequency of failed logon attempts so we can further investigate the high frequency of failed logon attempts. Additionally, we can also create a query to fetch logs from `SecurityEvents`. Here is a overview of all the KQL queries:

```sql title="Workbook - Timechart for Computer"
let domainAdmins = (_GetWatchlist("DomainAdmins") | project UserPrincipalName);
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == "4625"
| where TargetUserName in (domainAdmins)
| summarize arg_max(TimeGenerated, *) by TimeGenerated
| summarize count() by bin(TimeGenerated, 1h), Computer
| render timechart
```

```sql title="Workbook - Tiles for Computers"
let domainAdmins = (_GetWatchlist("DomainAdmins") | project UserPrincipalName);
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == "4625"
| where TargetUserName in (domainAdmins)
| summarize arg_max(TimeGenerated, *) by TimeGenerated
| summarize FailedLogons = count() by Computer
| sort by FailedLogons
```

```sql title="Workbook - Logs"
let domainAdmins = (_GetWatchlist("DomainAdmins") | project UserPrincipalName);
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == "4625"
| where TargetUserName in (domainAdmins)
| summarize arg_max(TimeGenerated, *) by TimeGenerated
| take 100
```

Microsoft Sentinel Workbooks can be seen as a feature that allows us to respond to incidents, alerts, and threats before they gain access to our environment. However, it can also be an excellent tool for detecting threats in our environment.

## Conclusion

Microsoft Sentinel Workbooks is designed to assist security analysts with better detecting malicious behaviors occurring in their environment. All organizations should setup multiple of workbooks to quickly detect suspicious behaviors and quickly respond to them.