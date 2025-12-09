---
title: "Enable Auto-Expanding Archiving for Exchange Online Mailboxes"
created: 2025-12-15
modified: 2025-12-15
description: "Exchange Online comes with the ability to archive emails up to 100 GB. However, with Microsoft E5 license the archive can be extended to 1.5 TB by enabling the Auto-Expanding Archiving feature in Microsoft Exchange Online."
tags: ["EXCHANGE"]
draft: true
---

## Introduction

The Chief Commerical Officer (CCO) recently contacted me about their archive storage in Exchange Online was close to 100 GB and requested a larger archive. I remembered that Microsoft E5 license comes with Auto-Expanding Archiving feature which allows users to archive emails up to 1.5 TB.

In this article, I'll go through enabling auto-expanding archiving feature for a specific user to help you solve those archive storage limits.

## Auto-Expanding Archive

You will need the Exchange Administrator role in Microsoft 365 environment to enable auto-expanding archiving feature.

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

Once the command is used the user should now have a auto-expanding archive mailbox which can hold up to 1.5 TB emails.

## Conclusion

The requirements to enable Auto-Expanding Archiving in Microsoft 365 is a Microsoft E5 license. I would recommend enabling Auto-Expanding Archive on heavy users such as CEO, CFO, CCO, and CTO as that will allow them to archive all their emails in-case it's needed in the future. Hopefully, this article helped you with enabling auto-expanding archiving feature.