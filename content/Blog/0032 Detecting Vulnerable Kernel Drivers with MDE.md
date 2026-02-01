---
title: "Detecting Vulnerable Kernel Drivers with MDE"
created: 2026-02-01
modified: 2026-02-01
tags: ["MDE", "SIEM"]
draft: true
---

## Introduction

I recently read through an article where threat actors where exploiting kernel driver vulnerabilities to escalate their privileges on endpoints. In this article I'll go through detecting these vulnerable drivers using Microsoft Defender XDR.


## Vulnerable Drivers

The [LOLDrivers Website](https://www.loldrivers.io/) has a complete list over all kernel drivers that are vulnerable for exploitation and that will allow them to elevate their privileges from user-mode to kernel (ring 0).

![[0031 Detecting-Vulnerable-Kernel-Drivers-with-MDE.png]]

```sql
 let LOLDrivers = externaldata (Category:string, KnownVulnerableSamples:dynamic, Verified:string ) [h@"https://www.loldrivers.io/api/drivers.json"]
     with (
       format=multijson,
       ingestionMapping=@'[{"Column":"Category","Properties":{"Path":"$.Category"}},{"Column":"KnownVulnerableSamples","Properties":{"Path":"$.KnownVulnerableSamples"}},{"Column":"Verified","Properties":{"Path":"$.Verified"}}]'
     )
| mv-expand KnownVulnerableSamples
| extend SHA1 = tostring(KnownVulnerableSamples.SHA1), SHA256 = tostring(KnownVulnerableSamples.SHA256);

DeviceEvents
| where ActionType == "DriverLoad"
| join kind=inner (LOLDrivers | where isnotempty(SHA256)) on SHA256
| union (
  DeviceEvents
  | where ActionType == "DriverLoad"
  | join kind=inner (LOLDrivers | where isnotempty(SHA1)) on SHA1
)
| summarize arg_max(DeviceName, *) by ActionType, FileName, FolderPath, SHA256
| project DeviceName, ActionType, FileName, SHA256
```

All the KQL does is it fetches the hashes from loldrivers.io website and then it goes through our DeviceEvents to see if any vulnerable drivers were loaded into our endpoints.

## Conclusion

Microsoft does come with a blocklist for vulnerable drivers but these blocklists are not updated in real-time and sometimes these drivers are never added to the blocklist which allows the threat actors to use these vulnerable kernel drivers to elevate their privileges.