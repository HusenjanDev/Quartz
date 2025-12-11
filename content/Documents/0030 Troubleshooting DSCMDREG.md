---
title: "Troubleshooting Microsoft Intune Devices"
created: 2025-12-22
modified: 2025-12-22
description: ""
tags: ["INTUNE"]
draft: true
---

## Information

When a device is onbaorded to Microsoft Intune the `dsregcmd` command can be used to understand the state of device in Microsoft Entra ID. In Microsoft Entra ID the device could be set to a different stage which can conflict with policies such as conditional access and etc...

| AzureAdJoined	| EnterpriseJoined | DomainJoined | Device state                   | 
| ------------- | ---------------- | ------------ | ------------------------------ |
| YES	        | NO               | NO           |	Microsoft Entra joined         |
| NO	        | NO               | YES          |	Domain Joined                  |
| YES           | NO               | YES          |	Microsoft Entra hybrid joined  |
| NO            | YES	           | YES	      | On-premises DRS Joined         |

The `dsregcmd` comes with the ability to show informations such as Device State, Device Details, Tenant Details, User State, SSO State, Diagnostic Data, and Ngt Prerequisite check which are great informations to have when there are issues with the device related to Microsoft Intune.

## Troubleshooting

When there is a issue occurring between the device and Microsoft Entra ID these are the commands which I recommend becoming familiar with as they will be extremely useful in these situations. 

The `dmsregcmd /status` command will show information about the device and the user. However, the information we are mostly interested in are `AzureAdJoined` and `DomainJoined` which will allow us to see if the device state is correct for our environment.

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

In some circumstances the device might need to be unjoined and rejoined from Microsoft Entra ID environment because a Windows update broke the configurations. Here are the commands which will allow us to perform a unjoin and rejoin on a device.

```powershell
# Unjoins the device from Microsoft Entra ID 
dsregcmd /leave

# Rejoins the device to Microsoft Entra ID
dsregcmd /join
```






