---
title: "How to Restore Deleted Teams Channel"
created: 2025-12-01
modified: 2025-12-01
description: "We can recover a Teams Channel without any issues as long it's within 30 days period but if it exceeds the 30 days period the Teams Channel is no longer recoverable."
tags: ["M365", "TEAMS"]
draft: false
---

## Introduction

Microsoft Teams comes with the ability to create multiple of channels where people can collaborate on different projects in the same group. When multiple of people has access to a Teams channel someone could accidentally delete it which can lead to data loss and productivity loss. When a Teams channel we should always investigate if the action were malicious or non malicious.

In this article I'll go through finding the user who deleted the Teams channel and method for recovering it.

## Restore Teams Channel

We can restore a Teams Channel as long it's within the 30 days period with the following steps.

1. Open Microsoft Teams.
2. Click on **Teams**.
    ![[0026 How-to-Restore-Deleted-Teams-Channel-00.png]]
3. Right click on **Teams group** and select **Manage Team**.
    ![[0026 How-to-Restore-Deleted-Teams-Channel-01.png]]
4. Click on **More...** and click on **Deleted**.
    ![[0026 How-to-Restore-Deleted-Teams-Channel-02.png]]
5. Click on **Restore**.
    ![[0026 How-to-Restore-Deleted-Teams-Channel-03.png]]

## Microsoft Teams Logs

Microsoft Purview allows us to investigate all the actions that were performed against a Teams channel such as delection, modifications, and etc...

1. Go to [Microsoft Purview Audit](https://purview.microsoft.com/audit).
    ![[0026 How-to-Restore-Deleted-Teams-Channel-04.png]]
2. Enter **Teams Channel** name on **Keywords Search**.
    ![[0026 How-to-Restore-Deleted-Teams-Channel-05.png]]
3. Click on **Search** and wait anywhere from 1 to 5 minutes.
    ![[0026 How-to-Restore-Deleted-Teams-Channel-06.png]]
4. Scoll down and click on **Search Name**.
    ![[0026 How-to-Restore-Deleted-Teams-Channel-07.png]]
5. You can click on the log and it will display user, action performed time, and much more..
    ![[0026 How-to-Restore-Deleted-Teams-Channel-08.png]]

## Investigation

When an Teams channel is deleted there should be an investigation on why the action occurred because it could lead to data loss. You should ask the following questions:

* Who deleted the Teams channel?
* When was the Teams channel deleted?
* Why did the user perform that action?
* Where there any malicious sign-in logs on the user who performed the action?
* Where there any other malicious actions performed by the user?

It's important to collect as much data as possible about the incident to ensure that the incident doesn't occur in the future.

## Prevention

A great way of preventing this incident from occurring in the future is by implementing an alerting system which detects when a Teams channel is deleted and sends a Teams message to all the owners to ensure it was a approved action. If the deletion of the Teams channel was not approved the owners could quickly recover it to prevent data loss and productivity loss.

## Conclusion

When a Teams channel is deleted recovering it is fairly easy as long it's within the 30 day period. However, if it exceeds the 30 days period the Teams channel is completely gone which leads to data loss and productivity loss which can cost the organization thousands of dollars.