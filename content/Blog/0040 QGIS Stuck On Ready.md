---
title: "QGIS 3.4 Stuck On Ready"
created: 2026-04-07
modified: 2026-04-07
tags: ["WINDOWS", "QGIS"]
draft: false
---

## Introduction

I pushed out Attack Surface Reduction Rules (ASR) to all endpoints in our organization and right after deploying it to the production a issue occurred with [QGIS 3.4](https://qgis.org/), which is a business application used by navigators. I spent hours troubleshooting the issue and found out that the issue was related to Azure File Share.

I decided to create this article to help someone who is experiencing the same issue with [QGIS 3.4](https://qgis.org/) application since I wasn't able to find anything in Google regarding it.

## Solution

So basically an inactive Azure Fileshare can interfere with QGIS application where it will lead to crashes. The only way to solve the issue is by removing the Azure Fileshare through following instructions.

1. Open **File Explorer** and then select **This PC**.

    ![[0040 QGIS-Stuck-On-Ready-01.png]]

2. Open **Terminal** as Administrator.

    ![[0040 QGIS-Stuck-On-Ready-02.png]]

3. Delete **Inactive Fileshare** using the following command.

    ![[0040 QGIS-Stuck-On-Ready-03.png]]

## Conclusion



I primarily work on Information Security related tasks but there are times where I'll need to dig into issues employees are experiencing. This was one of the cases where I implemented a policy which gave the impression that the ASR policy lead to QGIS stopping to work but it was actually related to an inactive Azure Fileshare.
