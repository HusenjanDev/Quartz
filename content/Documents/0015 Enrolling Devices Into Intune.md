---
title: "Enrolling Windows Device Into Intune"
created: 2025-10-01
modified: 2025-10-01
description: "Microsoft Intune enables us to manage devices remotely and there are multiple of ways for us to enroll devices."
keywords: ["Microsoft 365", "Enrolling Devices Into Microsoft Intune"]
tags: ["Microsoft Intune"]
draft: false
---

## Information

Microsoft Intune is the modern way of managing devices. In this article I'll go through multiple of ways to enroll a Windows device into Microsoft Intune.

## OOBE Automatic Enrollment

We can enroll a device into Microsoft Intune in OOBE stage with the following procedures.

1. Open `cmd.exe` with `SHIFT+F10`.
2. Enter `powershell.exe -ep bypass` inside the terminal.
3. Enter command  `Install-Script Get-WindowsAutopilotInfo -Force`.
3. To enroll the device use command `Get-WindowsAutopilotInfo -Online -GroupTag <TAG>`.
5. Login with company credentials.

Once these steps are completed after rebooting a Microsoft login page with company logo should come up.

## Hash Extraction Enrollment

We can also enroll a device into Microsoft Intune by extracting hashes.

1. Attach a USB into the device.
2. Open `cmd.exe` with `SHIFT+F10`
3. Enter `powershell.exe -ep bypass` inside the terminal.
4. Enter `Install-Script Get-WindowsAutopilotInfo -Force` to download Windows Autopilot.
5. Enter `Get-WindowsAutopilotInfo -GroupTag <TAG> -OutputFile D:\SERIALNUMBER.csv` to extract the hash.
6. Go to [Windows Autopilot Devices](https://intune.microsoft.com/#view/Microsoft_Intune_Enrollment/AutopilotDevices.ReactView/filterOnManualRemediationRequired~/false) and upload `SERIALNUMBER.csv` to Microsoft Intune.
7. Wait 15 minutes and reboot the system.
	![[0015 Enrolling-Devices-Into-Intune-01.png]]
Once that is completed next the device is rebooted the company login page will come up.

## Troubleshooting Issues

If you experience any issues during the enrollment of the system, I recommend checking out the following files.

* `C:\ProgramData\Microsoft\IntuneManagementExtension\Logs\AppWorkload.log`

## Conclusion

