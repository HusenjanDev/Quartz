---
title: "Enable Auto-Expanding Archiving"
created: 2025-12-15
modified: 2025-12-15
description: "Exchange Online enables users to archive emails up to 100 GB but that gap can be increased to 1.5 TB by enabling auto-expanding archive feature on their account."
tags: ["EXCHANGE"]
draft: true
---

## Introduction

The Chief Commerical Office (CCO) recently contacted me about their archive storage was close the 100GB and requested larger archive storage. I remembered that users with Microsoft E5 licenses comes with the Auto-Expanding Archiving feature which allows them to archive emails up to 1.5 TB.

In this article, I'll go through enabling auto-expanding archive for a specific user.

## Auto-Expanding Archive

You will need the Exchange Administrator role in Microsoft 365 environment in order to enable auto archiving.

1. Open PowerShell and [Microsoft Exchange Online Module](https://learn.microsoft.com/en-us/powershell/exchange/connect-to-exchange-online-powershell?view=exchange-ps).
    ```powershell
    Install-Module ExchangeOnlineManagement
    ```
2. Reopen PowerShell and connect to Microsoft Exchange Online.
    ```powershell
    Connect-ExchangeOnline
    ```
3. Login with the account that has **Exchange Administrator** role.
    ![[0028 Enabling Auto-Scaling-for-Exchange-Online-Archive-01.png]]

4. Use this command to see if Auto-Expanding Archiving is enabled on their account.
    ```powershell {"<EMAIL>"}
    Get-Mailbox <EMAIL> | FL AutoExpandingArchiveEnabled
    ```

5. Use this command to enable Auto-Expanding Archiving for the user.
    ```powershell {"<EMAIL>"}
    Enable-Mailbox <EMAIL> -AutoExpandingArchive
    ```


## Conclusion

The requirements to enable Auto-Expanding Archiving in Microsoft 365 is a Microsoft E5 license. I would recommend enabling Auto-Expanding Archive on users such as CEO, CFO, CCO, and CTO as that will allow them to archive all their emails incase it's needed in the future. Hopefully, this article helped you with enabling auto-expanding archiving on your users!