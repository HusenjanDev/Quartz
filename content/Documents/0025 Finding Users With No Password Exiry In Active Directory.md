---
title: "Finding Users With Password Never Expires In Active Directory"
created: 2025-11-27
modified: 2025-11-27
description: "Using PowerShell with Active Directory module you can search for users with Password Never Expire (PasswordNeverExpires) enabled."
tags: ["AD"]
draft: false
---

## Introduction

Many organizations nowadays has password policy to change the password frequently within a year period. However, there might be specific users who has option `PasswordNeverExpires` enabled which excludes them from the password policy. In this article I'll go through finding these users.

## Permissions

When a device is connected to the internal network the user can enumerate through all users using `net user husenjan /domain` and that will allow them to obtain informations such as `userPrincipalName`, `PwdLastSet`, and much more... To use the PowerShell script under you don't need any elevated privileges.

## PowerShell

The `ActiveDirectory` module from Microsoft enables us to enumerate through all our users in Active Directory environment using PowerShell. I decided to use that module to enumerate through all our users and find users who hasn't changed their password over a year.

```powershell hlt="<PATH>"
# Allowing high import of functions
$MaximumFunctionCount = 8192

# Relative Paths
$bergenCSV = "C:\<PATH>\Bergen_NoPasswordExpiry.csv"
$osloCSV =  "C:\<PATH>\Oslo_NoPasswordExpiry.csv"
$offshoreCSV =  "C:\<PATH>\Offshore_NoPasswordExpiry.csv"
$othersEnabledAccounts =  "C:\<PATH>\Others_EnabledAccounts_NoPasswordExpiry.csv"
$othersDisbaledAccounts =  "C:\<PATH>\Others_DisbaledAccounts_NoPasswordExpiry.csv"

# Importing ActiveDirectory library
Import-Module ActiveDirectory

# Getting all users
Get-ADUser -Filter * -Properties DisplayName, userPrincipalName, Office, PwdLastSet, Enabled | Select-Object DisplayName, userPrincipalName, Office, Enabled, PwdLastSet | ForEach-Object {
    # Creating variables with user data
    $displayName = $_.DisplayName
    $userPrincipalName = $_.userPrincipalName
    $PwdLastSet = [DateTime]::FromFileTime($_.PwdLastSet)
    $officeLocation = $_.Office
    $accountEnabled = $_.Enabled

    # If PwdLastSet is less than 2024 continue... 
    if ($PwdLastSet -lt (Get-Date).AddYears(-1)) {
        # Creating a PowerShell Object with all attributes enumerated through Get-ADUser function
        $userData = [PSCustomObject]@{
            displayName = $displayName
            userPrincipalName = $userPrincipalName
            PasswordLastSet = $PwdLastSet
            officeLocation = $officeLocation
            accountEnabled = $accountEnabled
        }

        # If Office equals Oslo then append user data to Oslo_NoPasswordExpiry.csv file
        if ($officeLocation -eq "Oslo") {            
            $userData | Export-Csv -Path $osloCSV -Append -NoTypeInformation -Encoding UTF8
        }
        # If Office equals Bergen then append user data to Bergen_NoPasswordExpiry.csv file
        elseif($officeLocation -eq "Bergen") {
            $userData | Export-Csv -Path $bergenCSV -Append -NoTypeInformation -Encoding UTF8
        }
        # If Office equals Offshore then append user data to Offshore_NoPasswordExpiry.csv file
        elseif($officeLocation -eq "Offshore") {            
            $userData | Export-Csv -Path $offshoreCSV -Append -NoTypeInformation -Encoding UTF8
        }
        # Any other users...
        else {
            # If their account is enabled append user data to Others_EnabledAccounts_NoPasswordExpiry.csv file
            if ($accountEnabled -eq $true) {
                $userData | Export-Csv -Path $othersEnabledAccounts -Append -NoTypeInformation -Encoding UTF8
            }
            # Otherwise append the user data to Others_DisbalededAccounts_NoPasswordExpiry.csv file
            else {
                $userData | Export-Csv -Path $othersDisbaledAccounts -Append -NoTypeInformation -Encoding UTF8
            }
        }
    }
}
```

What does the PowerShell script do? All the PowerShell script does is enumerating through all our users and finding users with no password change over a year and from there it filters the users depending on their office and exports the user data into the CSV file.

## Conclusion

We should avoid using `PasswordNeverExpires` on user accounts because the user password might not have been changed after a data breach occurred on a service provider and threat actors can use these passwords to lateral move through our environment. It's therefore recommended to disable `PasswordNeverExpires` on all user accounts.