---
title: "Deploying Cortex XDR through Intune"
created: 2025-09-26
modified: 2025-09-26
description: "How to use Cortex XDR to monitor Active Directory changes and updates"
keywords: ["Microsoft 365", "Red Team"]
tags: ["Microsoft Sentinel", "Kusto Query Language", "KQL"]
draft: true
---

## Information

Deploying Cortex XDR can be a difficult and frustrating task especially if you're deploying it through Microsoft Intune. So, I made this article to go through steps to deploy Cortex XDR through Microsoft Intune.

## Preperation

The preperation stage is about downloading Cortex XDR and converting the `.msi` to `.intunewin` file. The convertion from `.msi` to `.intunewin` enables us to configure a-lot of things and prevent things such as unexpected reboots. Here's a overview of downloading Cortex XDR:

**Cortex Panel-> Endpoint -> Agent Installation -> Create**.

![[0000 Cortex-XDR-through-Intune-01.png]]

**Enter Name and Description -> Select Latest Version Of Cortex -> Create**.

![[0000 Cortex-XDR-through-Intune-02.png]]

**Right Click -> 64-bit installer -> Download 64-bit installer (.msi)** 

![[0000 Cortex-XDR-through-Intune-03.png]]

Once Cortex XDR is downloaded use the following command to convert `.msi` to `.intunewin` using [Microsoft Content Preptool](https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool).

```powershell
.\IntuneWinAppUtil.exe -c "C:\Users\husenjan\Documents\Applications\Cortex" -s "C:\Users\hhesenjan\Documents\Applications\Cortex\Cortex 8.9.0.msi" -o "C:\Users\husenjan\Documents\Applications\Output"
```

## Deployment

The deployment stage is about uploading the `.intunewin` file into Microsoft Intune and from there configure the sections such as Program, Requirements, Detection Rules, and Assignments.

**Microsoft Intune -> Apps -> Platforms -> Windows**.

![[0000 Cortex-XDR-through-Intune-04.png]]

**Create Intune Application Profile -> Choose Windows App (Win32) -> Click on Select**

![[0000 Cortex-XDR-through-Intune-05.png]]

**Upload `Cortex-XDR.intunewin` appplication to Microsoft Intune**.

![[0000 Cortex-XDR-through-Intune-06.png]]

**Use the `/norestart` to prevent unexpected reboots**.

![[0000 Cortex-XDR-through-Intune-07.png]]

**Select "Yes, Specific systems the app can be installed on" -> Select "Install x86" -> Select "Install on x64"**.

![[0000 Cortex-XDR-through-Intune-08.png]]

**Select Use Custom Detection Script -> Upload Cortex-Detection-Rule.ps1**

```powershell title="Cortex-Detection-Rule.ps1"
$CortexVersion = [System.Version]::new((Get-ItemPropertyvalue -Path 'HKLM:\SOFTWARE\Cyvera\Client' -Name 'Product Version'))

if($CortexActualVersion -ge [System.Version]::(0)){
	Write-Output "Installation Failed"
	exit 0
} else {
	Write-Output "Installation Succesful"
	exit 1
}
```

![[0000 Cortex-XDR-through-Intune-09.png]]

**Inside Assignments -> Select All Devices**

![[0000 Cortex-XDR-through-Intune-10.png]]

Once all these steps are completed, Microsoft Intune should start installing Cortex XDR on all endpoints that are enrolled to the organization.

## Conclusion

Deploying Cortex XDR can quickly become a difficult task therefore I made the following tutorial to potentially help others out with installing Cortex XDR system into Microsoft Intune.
