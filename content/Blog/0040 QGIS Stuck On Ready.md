---
title: "QGIS Stuck On Ready"
created: 2026-03-29
modified: 2026-03-29
tags: ["WINDOWS", "ISSUE"]
draft: true
---

## Introduction

I recently pushed out Attack Surface Reduction Rules (ASR Rules) to all our endpoints and right after that a issue started occurring related to QGIS. I originally though the issue occurred because of our ASR rules but after troubleshooting for hours, I found out that the issue was related to Azure File Share (File-Share).

In this article I'll go through resolving the issue where QGIS is stuck at on ready stage.

## Instructions

1. Open **File Explorer** and then select **This PC**.

    ![[0040 QGIS-Stuck-On-Ready-01.png]]

2. Open **Terminal** as Administrator.

    ![[0040 QGIS-Stuck-On-Ready-02.png]]

3. Delete **Inactive Fileshare** using the following command.

    ![[0040 QGIS-Stuck-On-Ready-03.png]]

## Conclusion

