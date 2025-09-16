---
title: "Dynamic Group for Shared Mailboxes"
created: 2025-09-16
modified: 2025-09-16
description: "Dynamic Group for Shared Mailboxes"
keywords: ["Microsoft 365", "Red Team"]
tags: ["Microsoft 365", "Shared Mailboxes", "Dynamic Group"]
draft: false
---

## Information

The coordinator and crewing department in our organization was facing challenges with organizing their emails because there are 7 to 10 vessles taking contact into a single shared mailbox. This can quickly become difficult when there are 20 to 30 employees working on a single shared mailbox.

## Configuration

It was decided by coordinator and crewing department that there would seperate shared mailboxes for coordinator and crewing department for each vessels as that will allow the manager to allocate people to focus on specific vessels but crewing department would have access to coordinator shared mailboxes.

| Department | Shared Mailbox Format |
| ---------- | --------- |
| Coordinators | `Vesel-Coordinator@Organization.com` |
| Crewing      | `Vessel-Crew-Change@Organization.com` |

It was also decided that employees would only be able to send *"On Behalf Of"* emails from these shared mailboxes so the crews onboard on the vessels would know who to take contact with on Microsoft Teams. It was fairly easy to create and setup these shared mailboxes.

## User Access Management Concerns

An cybersecurity risk occurred because the coordinator and crewing department deals with a-lot of confidential documents such as passports, visas, and so forth. Assigning 20 to 30 employees to 20 different shared mailboxes becomes unmanageable and a massive security concern.

![[0000 Dynamic-Group-for-Shared-Mailbox-1.png]]

The solution that I came up with was to create two [Mail-Enabled Security Groups](https://learn.microsoft.com/en-us/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) called for **ACL-Vessel-Coordinator** and **ACL-Vessel-Crew-Change** which enables us to add users into these groups and the users will instantly get access to all the shared mailboxes they are supposed to have access to.

## Conclusion

In Microsoft 365 a security group cannot be added into a shared mailbox but mail-enabled security group can be added into shared mailbox and that enables us to add or remove users access from multiple of shared mailboxes. This is really useful if 20+ employees from same department needs access to 10 different shared mailboxes.