---
title: "Why PowerShell Automation Is Fantastic!"
created: 2025-12-20
modified: 2025-12-20
description: "High CPU usage during a scheduled scan with Microsoft Defender XDR means there is a misconfiguration in the Microsoft Defender Antivirus Policy. In this article I'll go through AvgLoadCPUFactor, Low CPU Priority, ScanOnlyIfIdleEnabled, and DisableCpuThrottleOnIdleScans to hopefully resolve the performance issue that you're experiencing with scheduled scans."
tags: ["POWERSHELL", "EXCHANGE"]
draft: true
---

```powershell
# Allow function imports
$MaximumFunctionCount = 8192

# Importing ActiveDirectory Module
Import-Module ActiveDirectory

# PowerShell Object
[PSCustomObject[]]$usersInfo = @()

# Getting users with PasswordNeverExpires
# Offices: Bergen, United Kingdom, Oslo, Houston, Penang
function Get-PasswordNeverExpiresUsers {
    param($Location)

    if ($Location -eq "Bergen") {
        Get-ADUser -Filter  {Office -eq "Bergen"} -Properties DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | Select-Object DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | ForEach-Object {
            if ($_.PasswordNeverExpires -eq $true) {
                $usersInfo += [PSCustomObject]@{
                    "Display Name" = $_.DisplayName
                    "User Principal Name" = $_.userPrincipalName
                    "Password Last Set" = [DateTime]::FromFileTime($_.PwdLastSet)
                    "Office" = $_.Office
                    "Password Never Expires" = $_.PasswordNeverExpires
                    "Account Enabled" = $_.Enabled
                }
            }
        }
        $usersInfo | Format-Table
    }
    elseif ($Location -eq "UK") {
        Get-ADUser -Filter  {Office -eq "Oslo"} -Properties DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | Select-Object DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | ForEach-Object {
            if ($_.PasswordNeverExpires -eq $true) {
                $usersInfo += [PSCustomObject]@{
                    "Display Name" = $_.DisplayName
                    "User Principal Name" = $_.userPrincipalName
                    "Password Last Set" = [DateTime]::FromFileTime($_.PwdLastSet)
                    "Office" = $_.Office
                    "Password Never Expires" = $_.PasswordNeverExpires
                    "Account Enabled" = $_.Enabled
                }
            }
        }
        $usersInfo | Format-Table
    }
    elseif ($Location -eq "Oslo") {
        Get-ADUser -Filter  {Office -eq "Oslo"} -Properties DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | Select-Object DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | ForEach-Object {
            if ($_.PasswordNeverExpires -eq $true) {
                $usersInfo += [PSCustomObject]@{
                    "Display Name" = $_.DisplayName
                    "User Principal Name" = $_.userPrincipalName
                    "Password Last Set" = [DateTime]::FromFileTime($_.PwdLastSet)
                    "Office" = $_.Office
                    "Password Never Expires" = $_.PasswordNeverExpires
                    "Account Enabled" = $_.Enabled
                }
            }
        }
        $usersInfo | Format-Table
    }
    elseif ($Location -eq "Penang") {
        Get-ADUser -Filter  {Office -eq "Penang"} -Properties DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | Select-Object DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | ForEach-Object {
            if ($_.PasswordNeverExpires -eq $true) {
                $usersInfo += [PSCustomObject]@{
                    "Display Name" = $_.DisplayName
                    "User Principal Name" = $_.userPrincipalName
                    "Password Last Set" = [DateTime]::FromFileTime($_.PwdLastSet)
                    "Office" = $_.Office
                    "Password Never Expires" = $_.PasswordNeverExpires
                    "Account Enabled" = $_.Enabled
                }
            }
        }
        $usersInfo | Format-Table
    }
    elseif ($Location -eq "Houston") {
        Get-ADUser -Filter  {Office -eq "Houston"} -Properties DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | Select-Object DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | ForEach-Object {
            if ($_.PasswordNeverExpires -eq $true) {
                $usersInfo += [PSCustomObject]@{
                    "Display Name" = $_.DisplayName
                    "User Principal Name" = $_.userPrincipalName
                    "Password Last Set" = [DateTime]::FromFileTime($_.PwdLastSet)
                    "Office" = $_.Office
                    "Password Never Expires" = $_.PasswordNeverExpires
                    "Account Enabled" = $_.Enabled
                }
            }
        }
        $usersInfo | Format-Table
    }
    elseif ($Location -eq "Offshore") {
        Get-ADUser -Filter  {Office -eq "Offshore"} -Properties DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | Select-Object DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | ForEach-Object {
            if ($_.PasswordNeverExpires -eq $true) {
                $usersInfo += [PSCustomObject]@{
                    "Display Name" = $_.DisplayName
                    "User Principal Name" = $_.userPrincipalName
                    "Password Last Set" = [DateTime]::FromFileTime($_.PwdLastSet)
                    "Office" = $_.Office
                    "Password Never Expires" = $_.PasswordNeverExpires
                    "Account Enabled" = $_.Enabled
                }
            }
        }
        $usersInfo | Format-Table
    }
    else
    {
        Get-ADUser -Filter  * -Properties DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | Select-Object DisplayName, userPrincipalName, Office, PwdLastSet, PasswordNeverExpires, Enabled | ForEach-Object {
            if ($_.PasswordNeverExpires -eq $true) {
                $usersInfo += [PSCustomObject]@{
                    "Display Name" = $_.DisplayName
                    "User Principal Name" = $_.userPrincipalName
                    "Password Last Set" = [DateTime]::FromFileTime($_.PwdLastSet)
                    "Office" = $_.Office
                    "Password Never Expires" = $_.PasswordNeverExpires
                    "Account Enabled" = $_.Enabled
                }
            }
        }
        $usersInfo | Format-Table
    }
}

function Get-DomainAdministators {
    Get-ADGroupMember "Domain Admins"  | Select-Object Name, SamAccountName | ForEach-Object {
        $samAccountName = $_.SamAccountName
        Get-ADUser -Filter "SamAccountName -eq '$($_.SamAccountName)'" -Properties DisplayName, userPrincipalName, SamAccountName, PwdLastSet, Enabled | ForEach-Object {
            $usersInfo += [PSCustomObject]@{
                "Display Name" = $_.DisplayName
                "User Principal Name" = $_.userPrincipalName
                "Password Last Set" = [DateTime]::FromFileTime($_.PwdLastSet)
                "Account Enabled" = $_.Enabled
            }
        }
    }

    $usersInfo | Format-Table
}

# Getting all group members
# GroupName = Required
# AccountStatus = $True or $False (Default $Null)
function Get-GroupMembers {
    [CmdLetBinding()]
    param (
        [Parameter(Mandatory = $true)] $GroupName,
        [Parameter] [Boolean] [AllowNull()] $AccountStatus
    )

    if ($AccountStatus -eq $True -or $AccountStatus -eq $False) {
        Get-ADGroupMember $GroupName | Select-Object Name, SamAccountName | ForEach-Object {
            Get-ADUser -Filter "SamAccountName -eq '$($_.SamAccountName)'" -Properties DisplayName, userPrincipalName, SamAccountName, Enabled | ForEach-Object {
                if ($_.Enabled -eq [System.Convert]::ToBoolean($AccountStatus)) { 
                    $usersInfo += [PSCustomObject]@{
                        "Display Name" = $_.DisplayName
                        "User Principal Name" = $_.userPrincipalName
                        "Account Enabled" = $_.Enabled
                    }
                }
            }
        }
    }
    else {
        Get-ADGroupMember $GroupName | Select-Object Name, SamAccountName | ForEach-Object {
            Get-ADUser -Filter "SamAccountName -eq '$($_.SamAccountName)'" -Properties DisplayName, userPrincipalName, SamAccountName, Enabled | ForEach-Object {
                $usersInfo += [PSCustomObject]@{
                    "Display Name" = $_.DisplayName
                    "User Principal Name" = $_.userPrincipalName
                    "Account Enabled" = $_.Enabled
                }
            }
        }
    }

    $usersInfo | Format-Table
}

# Getting Total Member Count
function Get-TotalGroupCount {
    [CmdLetBinding()]
    param(
        [Parameter(Mandatory = $true)] [String] $GroupName
    )

    $activeUsers = 0
    $nonActiveUsers = 0
    $totalUsers = 0
    
    Get-ADGroupMember $GroupName | Select-Object Name, SamAccountName | ForEach-Object {
        Get-ADUser -Filter "SamAccountName -eq '$($_.SamAccountName)'" -Properties DisplayName, userPrincipalName, SamAccountName, Enabled | ForEach-Object {
            if ($_.Enabled -eq $True) {
                $activeUsers++
            }
            elseif ($_.Enabled -eq $False){
                $nonActiveUsers++ 
            }
        }
        $totalUsers++

    }

    Write-Host $GroupName "Active Member Count:" $activeUsers
    Write-Host $GroupName "Non Active Members Count: " $nonActiveUsers
    Write-Host $GroupName "Total Members:" $totalUsers
}
```