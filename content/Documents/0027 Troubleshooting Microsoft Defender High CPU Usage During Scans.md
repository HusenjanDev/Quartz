---
title: "Troubleshooting Microsoft Defender XDR High CPU Usage During Scans"
created: 2025-12-05
modified: 2025-12-05
description: "High CPU Usage during scheduled scans with Microsoft Defender XDR means there is a configuration issue within our Microsoft Antivirus Policy. In this article, I'll go through the process of configuring AvgLoadCPUFactor and Enabling Low CPU Priority to prevent Microsoft Defender XDR from over consuming CPU during scheduled scans.s"
tags: ["MDE"]
draft: true
---

## Introduction

We recently migrated all our devices into Microsoft Defender XDR and some of our users have been experiencing performance issues when the weekly scheduled scan is running on their system. In this article I'll go through configuring the different features to hopefully help others who are experiencing similar issue.

## AvgLoadFactor & Low CPU Priority

**What is `AvgLoadFactor{:PowerShell}` in Microsoft Defender XDR?** The `AvgLoadFactor{:PowerShell}` is a configuration that can be set through a Microsoft Defender Antivirus Policy. It's used to specify the maximum precentage of the CPU usage for a scan but the `AvgLoadFactor{:PowerShell}` is not a hard limit but a guidance to the scanning engine. The `AvgLoadFactor{:PowerShell}` is also known as `ScanAvgCPULoadFactor{:PowerShell}` inside Windows operating system. 

**What is `Enable Low CPU Priority{:PowerShell}` in Microsoft Defender XDR?** The Low CPU Priority is a configuration that can be set through Microsoft Defender Antivirus Policy. It will ensure that Microsoft Defender scheduled scans are threated as low priority to ensure that it doesn't conflict with more important applications such as browsers, business applications, and etc...

We can configure `AvgLoadFactor{:PowerShell}` and `Enable Low CPU Priority{:PowerShell}` through the following steps.

1. Go to Microsoft Intune.
2. Go to Security Panel -> Antivirus Policy.
3. Create a new policy and configure the `AvgLoadFactor{:PowerShell}` to `15{:number}`.
4. Apply these changes.

Once the Microsoft Defender Antivirus Policy is applied to our machines we can use the following command to view the `AvgLoadFactor{:PowerShell}` and that value should be set to `15{:PowerShell}`. 

```powershell
Get-MpPreference | Select ScanAvgCPULoadFactor
```

If users are experiencing performance issues after these configurations are applied to their machine, I recommend reading through the next section as it goes through fixing the performance issues.

## Performance Issues

When `ScanOnlyIfIdleEnabled{:PowerShell}` and `DisableCpuThrottleOnIdleScans{:PowerShell}` options are set to `true{:PowerShell}` on the Windows machine the `ScanAvgCPULoadFactor{:PowerShell}` is ignored which can lead to performance issues. 

```powershell
Get-MpPreference | Select ScanAvgCPULoadFactor, DisableCpuThrottleOnIdleScans, ScanOnlyIfIdleEnabled
```

The `ScanOnlyIfIdleEnabled{:Powershell}` and `DisableCpuThrottleOnIdleScans{:PowerShell}` options can be disabled by creating creating a configuration profile on Microsoft Intune.

1. Go to Microsoft Intune Admin Center.
2. Click on Browser -> Configuration.
3. Select Platform as Windows 10 and later.
4. Select Profile as Settings catalog.
5. Provide name and description.
6. Click on Add settings.
7. Inside Settings Picker, search up **Idle**.
8. Select **Disable CPU Throttle On Idle Scans** and **Scan Only If Idle Enabled**
9. On **Scan Only If Idle Enabled** click on **Run scheduled scans regardless of whether the system is idle**.
10. On **Disable CPU Throttle On Idle Scan** turn it on.

Once the configuration profile is applied to the machines with High CPU Usage (CPU Throttles) these machines should no longer experience these performance issues from here on. 

## Conclusions

If your organization also uses scheduled scans with Microsoft Defender XDR it's recommended to use a low value on `AvgLoadFactor{:PowerShell}` and use the feature `Enable Low CPU Priority` as it will help with preventing users experiencing performance issues. If your users are still experiencing these performance issues it's recommended to disable the options `ScanOnlyIfIdleEnabled{:Powershell}` and `DisableCpuThrottleOnIdleScans{:PowerShell}` as they can interfere with with the `ScanAvgCPULoadFactor`.