---
title: "Deploying Notepad++ to Intune"
created: 2026-02-09
modified: 2026-02-09
tags: ["SIEM", "KQL"]
draft: true
---

## Information

Notepad++ infrastructure was recently compromised by state sponsored hackers which lead to some endpoints being compromised [*(You can read more about it here...)*](0032-notepad-security-incident-threat-hunting-using-kql). Notepad++ recommended us to update all our endpoints with the latest Notepad++ and in this article I'll go through doing that using Microsoft Intune.

## Intunewin

The Notepad++ application comes in `.exe` format which means we will need to create a `.intunewin` file using Microsoft Content Prep Tool. 

1. Download [Notepad++](https://notepad-plus-plus.org/)
2. Download [IntuneWinAppUtil](https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool)
3. Start **IntuneWinAppUtil** and enter the correct details.

    ![[0033 Deploying-Notepad-to-Intune-01.png]]

4. The **IntuneWinAppUtil** should generate a `.intunewin` file inside the output folder.
    ![[0033 Deploying-Notepad-to-Intune-02.png]]

## Deployment

1. Go to **[Microsoft Intune](https://intune.microsoft.com/)**
2. Go to **Apps -> Windows -> Windows Apps**

    ![[0033 Deploying-Notepad-to-Intune-03.png]]

3. Select **Windows App (Win32)**.

    ![[0033 Deploying-Notepad-to-Intune-04.png]]
4. Upload **notepad.intunewin file**.

    ![[0033 Deploying-Notepad-to-Intune-05.png]]

4. Enter **Name, Description, Publisher, App Version, and etc...**

    ![[0033 Deploying-Notepad-to-Intune-06.png]]

5. Use the following configuration on Program section.
    * **Install Command:** `notepad.exe /S` 
    * **UUninstall Command:** `"C:\Program Files\Notepad++\uninstall.exe" /S`.

    ![[0033 Deploying-Notepad-to-Intune-07.png]]

6. Use the following configuration for Requirements section.
    * **Check operating system architecture:** Yes
    * **Options:** `Install on x64 system`

    ![[0033 Deploying-Notepad-to-Intune-08.png]]

7. Select **Use a custom detection script** and use the following script.

    ```powershell 
    $notepad = Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Notepad++" -ErrorAction SilentlyContinue

    if ($notepad.DisplayVersion -eq "8.91") {
        exit 0
    }
    else {
        exit 1
    }
    ```

    ![[0033 Deploying-Notepad-to-Intune-09.png]]
    
8. Select **Groups** or **All Devices** that should have Notepad++ installed.

    ![[0033 Deploying-Notepad-to-Intune-10.png]]

9. Create the application profile.

## Conclusion
