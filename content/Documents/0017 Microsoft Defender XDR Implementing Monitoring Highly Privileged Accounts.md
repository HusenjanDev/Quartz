---
title: "Microsoft Defender XDR: Implementing Monitoring Highly Privileged Accounts"
created: 2025-10-20
modified: 2025-10-20
description: "Advanced Hunting in Microsoft Defender XDR enables us to create custom detection rules to create alerts when a specific set of actions occurs. In this article I'll go through creating custom detection rules to monitor highly privileged accounts."
keywords: ["Microsoft Defender", "Microsoft Defender for Endpoint", "Onboarding Microsoft Defender to 1000 Devices"]
tags: ["Microsoft Defender XDR", "KQL"]
draft: false
---

## Information

Advanced Hunting in Microsoft Defender XDR allows us to create Custom Detection Rules using Kusto Query Language (KQL) to create alerts and incidents inside of Microsoft Defender Portal. Today, I decided to create a custom detection rule to create an alert when a change occurs on a highly privileged account. In this article I'll go through the step-by-step guide to implement custom detection rules.

## Implementation

The `SecurityEvent` table contains event logs from Active Directory. The [Event ID 4738](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4738) occurs when a user account is updated and [Event ID 4723](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4723) occurs when a password reset has happened on a account. Using all these information's we can craft the following KQL:

```sql
SecurityEvent
| where TargetUserName  in  ("husenjan-admin", "john-admin")
| where Account !has "MSOL" 
| where Account != "NT AUTHORITY\\ANONYMOUS LOGON"
| where EventID in (4738, 4723) 
| summarize arg_max(TimeGenerated, *) by EventRecordId
| sort by TimeGenerated desc
| where TimeGenerated > ago(2h)
```

The query is getting all the events that has occurred on users `husenjan-admin` and `john-admin`. It's then filtering out the data where the actions where performed by `MSOL` and `NT AUTHORITY\ANONYMOUS LOGON` because these are expected behaviors. It's then getting all log data which has event id of 4723 and 4738. Now since we are familiar with KQL query let's implement custom detection rule through Microsoft Defender Portal.

1. Go to Microsoft Defender -> Investigation and Response -> Advanced Hunting.
    ![[0000 Microsoft-Defender-XDR-Monitoring-Highly-Privileged-Accounts-01.png]]

2. Write KQL query and then go to Create Detection Rule -> Create Custom Detection Rule.
    ![[0000 Microsoft-Defender-XDR-Monitoring-Highly-Privileged-Accounts-02.png]]

3. Write short Description Name and Rule Description from there configure Frequency to execute KQL query each 15 minutes.

    ![[0000 Microsoft-Defender-XDR-Monitoring-Highly-Privileged-Accounts-03.png]]

4. Inside Alert Settings create the Alert Title and Description using `{{TargetUserName}}` and `{{SubjectUserName}}` as that will allow us to see who performed the action in alert.

    ![[0000 Microsoft-Defender-XDR-Monitoring-Highly-Privileged-Accounts-04.png]]

5. Scoll down inside Alert Settings, and create two entities and use `{{TargetSid}}` and `{{SubjectUserSid}}` to reference accounts inside the alert - this will allow us to see affected user and user who performed the action.

    ![[0000 Microsoft-Defender-XDR-Monitoring-Highly-Privileged-Accounts-05.png]]

6. Inside Automated Actions you can choose specific actions to perform - in my case nothing needs to be performed.

    ![[0000 Microsoft-Defender-XDR-Monitoring-Highly-Privileged-Accounts-06.png]]

7. Review the settings and create the custom detection rule.

    ![[0000 Microsoft-Defender-XDR-Monitoring-Highly-Privileged-Accounts-07.png]]

8. Once the custom detection rule is created you will receive the following alert when a change occurs on a highly privileged account.

    ![[0000 Microsoft-Defender-XDR-Monitoring-Highly-Privileged-Accounts-08.png]]

## Conclusion

Advanced Hunting in Microsoft Defender XDR is exceptional as it comes with the ability to create Custom Detection Rules to identify threats early on. When an incident occurs in the future we can create custom detection rules to respond early on when it occurs next time. It's also important to note that we can create custom detection rules for tables such as `DeviceEvents`, `DeviceProcessEvents`, `BehaviorAnalytics`, and much more.