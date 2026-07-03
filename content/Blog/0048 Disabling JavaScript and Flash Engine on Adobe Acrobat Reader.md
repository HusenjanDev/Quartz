---
title: "Disable JavaScript and Flash Engine in Adobe Reader"
created: 2026-07-03
modified: 2026-07-03
tags: ["Windows", "MDE", "PowerShell"]
draft: false
---

## Introduction

Adobe Acrobat Reader is one of the most commonly used applications used for reading PDF documents. One thing that most people are not aware of is that Adobe Acrobat Reader comes with support for JavaScript and also the Flash engine. Majority of vulnerabilities happens because of these two features that is why Microsoft recommends to disable these two features. 

Additionally, disabling JavaScript and Flash engine will gain us extra 0.66% secure score. In this article I'll go through disabling JavaScript and Flash engine for Adobe Acrobat Reader so your environment is more secure.

## PowerShell

So basically I built a PowerShell script which disables JavaScript and Flash engine by creating or updating the registry values inside of the Adobe Acrobat Reader registry directory. I also built a function which deletes the registry values incase it distrupts the business *(Honestly who uses JavaScript and Flash Engine on Adobe in 2026)*...

```powershell title="DisableJavaScriptAndFlashEngine.ps1"
function DisableJavaScript() {
    $adobe_reader_feature_reg_path = "HKLM:\Software\Policies\Adobe\Acrobat Reader\DC\FeatureLockDown"

    Write-Host "[#] Running the ApplyDisableJavaScript function."
    if (-not (Get-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bDisableJavaScript" -ErrorAction SilentlyContinue)) {
        # Create bDisableJavaScript with number 1
        New-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bDisableJavaScript" -Value 1 | Out-Null
        Write-Host "[#] Created Registry: " $adobe_reader_reg_path "bDisableJavaScript" 
    }
    elseif ((Get-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bDisableJavaScript" -ErrorAction SilentlyContinue)) {
        # Updates bDisableJavaScript value to 1
        Set-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bDisableJavaScript" -Value 1 | Out-Null
        Write-Host "[#] Changed registry for bDisableJavaScript to 1."
    }
    else {
        Write-Host "[!] Unknown the bDisableJavaScript couldn't be created or updated."
    }
}

function DisableFlash() {
    # Initialization Variables
    $adobe_reader_feature_reg_path = "HKLM:\Software\Policies\Adobe\Acrobat Reader\DC\FeatureLockDown"

    Write-Host "[#] Running the ApplyDisableFlash function."

    # Is bEnableFlash created already? 
    if (-not (Get-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bEnableFlash" -ErrorAction SilentlyContinue)) {
        # Creates the registry value and disables Flash.
        New-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bEnableFlash" -Value 0 | Out-Null
        Write-Host "[#] Created registry for bFlash with value 0."
    }
    elseif ((Get-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bEnableFlash" -ErrorAction SilentlyContinue)) { 
        # Updates the registry value and disables Flash.
        Set-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bEnableFlash" -Value 0 | Out-Null
        Write-Host "[#] Changed registry for bFlash to 0."
    }
    else {
        Write-Host "[!] Unknown reason the bFlashEnable couldn't be created or updated."
    }
}

function RevertChanges() {
    # Adobe registry feature path
    $adobe_reader_feature_reg_path = "HKLM:\Software\Policies\Adobe\Acrobat Reader\DC\FeatureLockDown"

    # Deleting bEnableFlash on Adobe Acrobat Reader
    if ((Get-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bEnableFlash" -ErrorAction SilentlyContinue)) {
        Remove-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bEnableFlash"
    }

    # Deleting bDisableJavaScript on Adobe Acrobat Reader
    if ((Get-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bDisableJavaScript" -ErrorAction SilentlyContinue)) {
        Remove-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bDisableJavaScript"
    }
}


function main() {
    $adobe_reader_path = "HKLM:\Software\Policies\Adobe\Acrobat Reader"
    if ((Test-Path -Path $adobe_reader_path) -eq $true) {
        DisableFlash
        DisableJavaScript
    }
}

main
```

```powershell title="DetectionScript.ps1"
function DetectionFunc() {
    # Adobe registry feature path
    $adobe_reader_feature_reg_path = "HKLM:\Software\Policies\Adobe\Acrobat Reader\DC\FeatureLockDown"

    if ((Get-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bDisableJavaScript" -ErrorAction SilentlyContinue).bDisableJavaScript -eq 1) {
        Write-Host "[#] JavaScript is disabled."
        if ((Get-ItemProperty -Path $adobe_reader_feature_reg_path -Name "bEnableFlash" -ErrorAction SilentlyContinue).bEnableFlash -eq 0) {
            Write-Host "[#] Flash engine is disabled."
            Exit 0
        }
        else {
            Write-Host "[#] Flash engine is not disabled"
            Exit 1
        }
    }
    else {
        Write-Host "[#] JavaScript is not disabled."
        Exit 1
    }
}

DetectionFunc
```

The `DisableJavaScript` function is responsible for disabling JavaScript while the `DisabelFlash` is responsible for disabling Flash Engine. While the `RevertChanges` function is responsible for undoing the restriction applied by these two functions. The `DetectionScript.ps1` is mainly used for detecting if the changes applied successfully.

## Remediation Script

This section of the document goes through disabling JavaScript and Flash Engine using Remediation Script feature in Microsoft Intune.

1. Go to [**Microsoft Intune**](http://intune.microsoft.com/)

2. Click on **Devices -> Windows**.
    ![[0048 Disabling JavaScript and Flash Engine on Adobe Acrobat Reader 01.png]]

3. Click on **Scripts & Remediations**
    ![[0048 Disabling JavaScript and Flash Engine on Adobe Acrobat Reader 02.png]]

4. Enter the **Name and Description**.
    ![[0048 Disabling JavaScript and Flash Engine on Adobe Acrobat Reader 03.png]]

5. Upload the **DetectionScript.ps1** and **DisableJavaScriptAndFlashRemediationScript.ps1**.
    ![[0048 Disabling JavaScript and Flash Engine on Adobe Acrobat Reader 04.png]]

6. Select **Assignments** to all.
    ![[0048 Disabling JavaScript and Flash Engine on Adobe Acrobat Reader 05.png]]

7. Review the configuration and create the remediation script.
    ![[0048 Disabling JavaScript and Flash Engine on Adobe Acrobat Reader 06.png]]

Well congratulations! Once the PowerShell script is ran on the different devices the secure score should increase by 0.66% over a week.

## Conclusion

Adobe Acrobat is a great product for reading PDF documents but majority of the vulnerabilities that are found in the product is related to JavaScript and Flash Engine. This is why Microsoft recommends us to disable JavaScript and Flash Engine to gain 0.66% higher secure score. Hopefully, this article has assisted you with securing your environment.
