---
title: "Onbaording Microsoft Defender to 1000 Devices"
created: 2025-10-01
modified: 2025-10-01
description: "In this article I'll go through the steps that I took to onboard Microsoft Defender to 1500 devices from Cortex XDR."
keywords: ["Microsoft 365", "Enrolling Widnows Device Into Microsoft Intune"]
tags: ["Microsoft Intune", "Microsoft Defender", "Cortex XDR"]
draft: true
---

## Information

I was recently tasked with onboarding Microsoft Defender XDR to the organization as our Cortex XDR license was about to expire in 10 days. I was luckily prepared for it because a week before I was working on onboarding few devices to Microsoft Defender to see if it's good for the organization.

## Planning

We have multiple of offices in our organization based on Norway, United Kingdom, United States, and Malaysia. To reduce the chances of a issue occurring we decided to do the following:

1. Tuesday - Morning - Onboard 20% of Norway
2. Tuesday - Afternoon - Onboard 50% of Norway
3. Tuesday - Morning - Onboard 20% of Norway
4. Tuesday - Afternoon - Onboard 50% of United Kingdom
5. Wenesday - Morning - Onboard 20% of United States
6. Wenesday - Afternoon - Onboard 50% of United States
7. Thursday - Onboard - 100% of Norway
8. Thursday - Onboard - 100% of United Kingdom

This method ensures that we don't take out a whole department and if something did go wrong we can quickly revet back...

## Preperation

I really love to automate things but to onboard Microsoft Defender from Cortex XDR is difficult since there is no APIs to perform uninstallation in Cortex XDR. Therefore I made the following script which allows us to extract tsv file from Cortex XDR and automatically add it into a security group.

```powershell
# Parameters
$InputFileName = $args[0]
$OutputFileName = $args[1]
$GroupName = "ACL-MicrosoftDefender"

function main {
    if ($InputFileName -eq $null -or $OutputFileName -eq $null) {
        Write-Host "Please enter the command correctly: .\DeviceAdd.ps1 <CORTEX_FILE> <OUTPUT_FILE>"
        return 0
    }

    # Reading data from the $inputFile
    Import-Csv $InputFileName -Delimiter "`t" | ForEach-Object {
        # Getting device name
        $deviceName = @($_.PSObject.Properties)[0].Value.Trim() -replace "'", "''"

        # Sanitizing DeviceName
        $sDeviceName = $deviceName -replace "'", "''"

        # Getting the username
        $username = @($_.psobject.Properties)[18].Value

        # Sanitizing username
        $sUsername = $username -replace "'", "''"

        # Getting information about group through Microsoft Graph
        $group = Get-MgGroup -Filter "displayName eq '$GroupName'"

        # Getting information about device through Microsoft Graph
        $device = Get-MgDevice -Filter "startswith(displayName,'$sDeviceName')" -All -ErrorAction Stop

        # Splitting device ids
        $deviceIds = $device.Id.Split(" ")

        # Adding devices into the group
        for ($i = 0; $i -lt $deviceIds.Count; $i++) {
            # Creating odata ms.graph.microsoft.com + deviceId
            $odata = "https://graph.microsoft.com/v1.0/directoryObjects/"+$deviceIds[$i]
            # Try and catch for error messages
            try {
                # Adding device into the group
                New-MgGroupMemberByRef -GroupId $group.Id  -OdataId $odata -Erroraction Stop
                Write-Host "Added" $sDeviceName "into" $GroupName
            } catch [System.Exception] {
                # Catching error messages
                if($psitem.exception.message -like "*already exist*") {
                    Write-Host $deviceName "is already member of the group."
                }
            }
        }

        # Exporting device information to <OUTPUT_FILE>
        Get-MgDevice -Filter "startswith(displayName,'$sDeviceName')" -All -Erroraction Stop | Select-Object @{n='InputName';e={$sDeviceName}}, @{n='UserName';e={$userName}}, @{n='ObjectId';e={$_.Id}}

    } | Export-Csv -Path $OutputFileName -NoTypeInformation -Encoding UTF8
}

main
```

This script allows us to use Computer Name to obtain the Device ID from Microsoft Intune which then allows us to bulk import using object id.

## Endpoint Detection and Response Policy

The Endpoint Detection and Response Policy allows us to onboard devices into Microsoft Defender without having to execute a script. To create a Endpoint Detection and Response Policy follow the following instructions:

1. Microsoft Intune -> Endpoint Security -> Endpoint Detection and Response
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-01.png]]
2. Create Policy -> Platform (Windows) -> Profile (Endpoint Detection and Response)
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-02.png]]
3. Enter Policy Name and Policy Description
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-03.png]]
4. In configuration settings use the following configuration.
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-04.png]]
5. Select your organization scope tags.
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-05.png]]
6. Select the security group with devices inside of it.
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-06.png]]
7. Review the configuration and create it.
     ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-07.png]]

Once the policy is created we can now start adding devices into the ACL-MicrosoftDefender group.

## Onboarding Devices to Microsoft Defender

We cam now start onboarding devices to Microsoft Defender by doing the following procedures.

1. Cortex XDR -> Endpoint Groups.
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-08.png]]
2. Create a Cortex XDR Group with all the devices to onboard to Microsoft Defender.
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-09.png]]
3. Export the device list.
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-10.png]]
4. Use the PowerShell script above in the following way.
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-11.png]]
5. Inside Azure Portal we can see that these devices where successfully added into ACL-MicrosoftDefender
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-12.png]]

Once these devices are added into the security group they will start onboarding themselves into Microsoft Defender. It's possible to view the statestics through Microsoft Intune.

![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-13.png]]

## Cortex Removal

Once these devices has successfully installed Microsoft Defender we can then continue with uninstalling through the following procedure.

1. Cortex XDR -> Endpoint Groups
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-14.png]]
2. Select all devices -> Endpoint Control -> Unisntall Agents
    ![[0000 Onboarding-Microsoft-Defender-to-1000-Devices-15.png]]

We have now successfully uninstalled Cortex XDR from all these systems that has been onboarded to Microsoft Defender.

## Conclusion
