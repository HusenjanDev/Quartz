---
title: "Automatically Update Apps through Intune (WinGet)"
created: 2026-06-26
modified: 2026-06-26
tags: ["Windows", "Intune", "WinGet"]
draft: true
---

## Introduction

Microsoft Intune is a great way of managing devices to ensure they receive the necessary applications and policies to be productive and secure. It also helps with reducing the time IT-Support spends on setting up the device. However, one of the biggest issues with Microsoft Intune is updating the applications since a new application profile has to be created and tested which takes a-lot of time.

Instead of consistently creating application profiles we can instead perform automatic updates through WinGet which is a package manager that comes with Windows by default. In this article I'll be digging into ways you can automatically update all applications using WinGet.

## Understanding WinGet

WinGet is a package maanger which allows us to install and update applications through the command line. This saves us a-lot of time since if we were going to install an application we would need to go to website and then click on multiple of buttons. Instead of doing all these tasks we can instead use a command to install Visual Studio as an example:

```powershell title="WinGet: Visual Studio"
winget install -e --id Microsoft.VisualStudioCode
```

You can view all the applications that can be installed through WinGet through the following site [WInstall](https://winstall.app/). I usually perform a factory reset once a year and WinGet has been a life saver since it allows me to install all the applications I use day-to-day in seconds.

## Updating Application

WinGet also comes with the capability to update applications one-by-one or updating all of them. This allows us to update applications on the devices without spending thousands of dollars on patch management solution which synchronizes with Microsoft Intune.

```powershell title="WinGet: One-by-One or All"
# Get list of applications.
winget list

# Update specific application on device.
winget upgrade google.chrome --silent --accept-source-agreements --accept-package-agreements

# Update all applications on device.
winget upgrade --all --silent --accept-source-agreements --accept-package-agreements
```

We can execute these commands on the users devices using Remedication Scripts in Microsoft Intune. I would recommend running these commands once a day in the middle of the day or early in the morning while people are getting their coffee. 

> [!IMPORTANT]- Legacy Applications
> It's important to note that WinGet will update the application automatically in the background therefore it's crucial that you understand your environment since in some situations users in the organization might be using older version of the application since a specific feature is necessary for them to perform their work.

## Microsoft Intune