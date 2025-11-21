---
title: "Investigating Security Incidents with Microsoft Defender XDR"
created: 2025-11-24
modified: 2025-11-24
description: ""
keywords: ["Microsoft Defender XDR", "Microsoft Sentinel", "Microsoft Defender for Endpoint", "Security Incidents", "Investigating Seucirty Incidents with Microsoft Defender XDR"]
tags: ["MDE", "SIEM"]
draft: true
---

## Information

Investigating security incidents can become overwhelming as there is a-lot of things to learn such as Windows Operating System, PowerShell Scripting, Kusto Query Language, and also understanding the different features and capabilities that comes with Microsoft Defender XDR.

Additionally, it's also important to be confident with reading log files and create your own methodology for investigating security incidents. In this article I'll go through all these to hopefully help others.

## The 5 Pillars

When a security incident occurs in any XDR products I follow the following five pillars. 

* **Investigation:** Once a security incident occurs I'll try to collect as much data about the security incident such as SHA-256, Actions, Modifications, IP-Address, Email Address, and other information resources, users, and devices that has been affected by the security incident. 
* **Decission:** Using all the data collected from the investigation phase I'll decide whether to isolate computer, disable user account, or perform a full scan on the system.
* **Prevention:** At the prevention stage I'll ensure that the SHA-256 and the IP-Address is added into Indication Of Compromise (IoCs) list. While also creating custom detection rule to detect the SHA-256 and IP-address to quickly isolate devices that are affected.
* **Recovery:** This stage consists of re-imaging the laptop and re-enabling the user account so they can start working again.
* **Documentation:** I'll create a documentation about the security incident with all the data collected from the incident including timeline. Additionally, I'll include a lesson learnt section to see where we can do better.

Nowadays an enterprise has over hundreds to thousands of applications that the organization uses therefore it's important as a security analysts to understand our environment.

## Artifical Intelligence

Artificial Intelligence (AI) is beneficial for security analysts as it can help us with searching through our notes, and documents to find the information's that we are searching after. As an example you remember investigating an application with name `AWSConnector.exe` and created notes for it and instead of manually going through your notes you can easily find it with artifical intelligence. 

Use Artificial Intelligence (AI) to increase your productivity because it's never going away now. The people who uses AI will move forward while the people who are not using it will fall behind... 

## Investigation Phase 

### Advanced Hunting

When a malicious action occurs such as a user stealing company data, clicking on phishing links, and a device connecting to a command and control center the Advanced Hunting section can come in an excellent use as it allows us to see all data, users, and devices that were affected.

### VirusTotal

VirusTotal is a application that is widely used for scanning executable programs and other file types. It will scan the application against multiple of extended detection and response (XDR) systems to see if any of them detects the file as malicious.

### Collect Investigation Logs

When we are confident that a malicious application has compromised the system we should collect investigation log because these enables us to look through network connections, startup processes, running processes, and registry keys to better understand the threat. 

## Decission Phase 

### Antivirus Scan

When a malicious executable program is detected on a system we should perferrably run a full antivirus scan on the system to ensure it's safe. 

### Restricting App Execution



### Isolation

When it's confirmed that the system is compromised and all necessary investigation data is collected from the [[#Investigation Phase]] we can move forward with isolating the computer.


### Disable User Account

When a decision has been made to isolate the computer it's important that we also disable the user account to the investigation is completed because the threat actors might use the account for laterally moving through our environment.

## Prevention Phase

### IoCs

We should always add the SHA-256 and IP-Address to the indication of compromise list as it enables our XDR system to block the application and IP-address.

### Custom Detection Rule

We can also create a custom detection rule to isolate the computer and disable the user account when we see the SHA-256 or see an connection established with the malicious IP-Address.

## Recovery Phase

At the following phase we will focus on reimaging the comptuer of the compromised user and re-enabling their account. Additionally, it's extremely important to communicate with the user that they shouldn't be worried about the situation as it's part of our security protocol and that these security incidents can happen to anyone.

## Documentation Phase

At this stage I'll create a document including only relevant data from the four different pillars including a timeframe of all actions that were performed and also include lesson learnt section for what we could do better in the future.


## Conclusion