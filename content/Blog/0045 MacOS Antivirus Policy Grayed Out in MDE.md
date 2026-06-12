---
title: "MacOS Antivirus Policy Grayed Out in MDE"
created: 2026-06-12
modified: 2026-06-12
tags: ["MDE", "Antivirus", "MacOS"]
draft: false
---

## Introduction

In Microsoft Defender for Endpoint there are two types of antivirus policies for MacOS endpoints where one of them are legacy and the other one is newer as it allows us to configure features such as cloud protection, scheduled scanning, archive bomb, and potentially unwanted applications.

Whenever I tried to create the modern antivirus policy for MacOS endpoints the **"Next"** button was grayed out and wouldn't let me proceed with creating the antivirus policy. In this article I'll go through solving that issue.

## Solution

In this section I'll go through implementing modern Microsoft Defender Antivirus policy for MacOS endpoints.

1. Go to [Microsoft Intune](http://intune.microsoft.com)

2. Click on **Endpoint Security -> Antivirus**.
    ![[0045 MacOS Antivirus Policy Grayed Out in MDE 01.png]]

3. Click on **Create** then select **MacOS -> Microsoft Defender Antivirus**.
    ![[0045 MacOS Antivirus Policy Grayed Out in MDE 02.png]]

3. Enter the **Name and Description**.
    ![[0045 MacOS Antivirus Policy Grayed Out in MDE 03.png]]

4. After configuring the **Antivirus Engine** section the **"Next"** button is disabled.
    ![[0045 MacOS Antivirus Policy Grayed Out in MDE 04.png]]

5. The **"Next"** button will be disabled until the **"Allow Threats"** is configured with a single entry.
    ![[0045 MacOS Antivirus Policy Grayed Out in MDE 05.png]]

So basically the **"Next"** button will be disabled until someone configures the **"Allow Threats"** with minimum a single entry otherwise it will not allow us to create the Microsoft Defender Antivirus Policy for MacOS. Unfortunately, I don't know the reasoning why Microsoft demands minimum a single entry...

## Recommendations

I would recommend configuring Microsoft Defender Antivirus Policy for MacOS endpoints with the following configurations to secure them.

| Section | Setting | Value |
|---|---|---|
| **Cloud delivered protection** | | |
| | Cloud delivered protection | Enabled |
| | Automatic sample submission | Enabled |
| | Diagnostic collection level | required |
| | Automatic security intelligence updates | Enabled |
| | Automatic sample submission consent | all |
| | Security intelligence update due (days) | 1 |
| **Tamper protection** | | |
| | Enforcement level | block |
| **Features** | | |
| |  Behavior monitoring | enabled |
| | Scheduled scan | enabled |
| | Offline SI updates signature verification | enabled |
| | Performance profiles | enabled |
| **Scheduled scan** | | |
| | Check for definitions update | Enabled |
| | ↳ Time of day | 12 |
| | Daily/hourly quick scan configuration | — |
| | ↳ Start time | 0 |
| | Low priority scheduled scan | Enabled |
| | Ignore exclusions | Enabled |
| **Network protection** | | |
| | Enforcement level | block |
| **Antivirus engine** | | |
| | Real-time protection *(deprecated)* | Enabled |
| | Passive mode *(deprecated)* | Enabled |
| | Exclusions merge | admin_only |
| | Threat type settings merge | admin_only |
| | Allowed threats | NULL |
| | File hash computation | False |
| | Run scan after definitions update | Enabled |
| | Scan inside archive files | True |
| | Enforcement level | passive |
| | Offline security intelligence updates | disabled |
| | Fallback to Microsoft cloud updates | Enabled |
| **Threat Type** | | 
| | Potentially Unwanted Application | Block | 
| | Archive Bomb | Block |

Once the configurations are applied successfully to MacOS endpoints without any issues I would enable randomized scheduled scanning as it will help with detecting threats who are more complex.

## Conclusion

The modern version of Microsoft Defender Antivirus policy comes with more configurations which allows us to secure our MacOS endpoints. However, to  create the configuration the **"Allow Threats"** feature must minimum have a single entry otherwise the **"Next"** button will be grayed out and it won't let us create the policy. 
