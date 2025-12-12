---
title: "Troubleshooting Microsoft Entra ID Joined Devices"
created: 2025-12-22
modified: 2025-12-22
description: ""
tags: ["INTUNE"]
draft: true
---

## Information

When a device is onboarded to Microsoft Intune the `dsregcmd` command can be used to understand the state of device in Microsoft Entra ID. In Microsoft Entra ID the device can be in multiple of different stages.

| AzureAdJoined	| EnterpriseJoined | DomainJoined | Device state                   | 
| ------------- | ---------------- | ------------ | ------------------------------ |
| YES	        | NO               | NO           |	Microsoft Entra joined         |
| NO	        | NO               | YES          |	Domain Joined                  |
| YES           | NO               | YES          |	Microsoft Entra hybrid joined  |
| NO            | YES	           | YES	      | On-premises DRS Joined         |

The `dsregcmd` also shows us information about the device such as Device State, Device Details, Tenant Details, User State, SSO State, and Diagnostics Data, and Ngt Prerequisities which are great information to have when there are issues between our device and Microsoft Entra ID.

## Device Info

The `dsregcmd /status` command will show all the information about the device that is in the Microsoft Entra ID environment. Additionally, it will provide us with information if there are any issues between device and Microsoft Entra ID.

```powershell {6-8}
C:\Users\husenjan> dsregcmd /status
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HUSENJAN
           Virtual Desktop : NOT SET
               Device Name : Norway-PC001.int.husenjan.com
```

All the information shown with the `dsregcmd` command can be useful to troubleshoot the issue that is occurring in our environment but the user has to be technical.

## Leaving & Joining

In some circumstances the device might need to be unjoined and rejoined from Microsoft Entra ID environment because a Windows Update or something may have modified or deleted the registry keys.

```powershell
# Unjoins the device from Microsoft Entra ID 
dsregcmd /leave

# Rejoins the device to Microsoft Entra ID
dsregcmd /join

# Automatically unjoins and rejoin the device on Microsoft Entra ID
dsregcmd /forcerecovery
```

It's worth noting that when the device is unjoined and rejoined a login prompt requesting credentials will occur which requires valid credentials. If there are any issues with executing any of these commands the `/debug` parameter can be used to trobuleshoot why the command is failing.

## Single Sign On



## Tenant



## Conclusion


