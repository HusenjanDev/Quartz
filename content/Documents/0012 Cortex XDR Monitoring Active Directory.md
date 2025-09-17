---
title: "Cortex XDR Monitoring Active Directory"
created: 2025-09-18
modified: 2025-09-18
description: "How to use Cortex XDR to monitor Active Directory changes and updates"
keywords: ["Microsoft 365", "Red Team"]
tags: ["Cortex XDR", "Active Directory"]
draft: false
---

## Information

We recently experienced a incident where a company administrator changed the `userPrincipalName` of someone in the management. This lead to a investigation where we needed to find out who made the changes and the reasoning behind it to ensure that organization is secure.

## Mitigation

Once it was confirmed that the `userPrincipalName` of someone in the management the following actions where performed on the account.

1. Account was disabled through Active Directory
2. Monitoring all sign-ins occurred in Microsoft Entra and Active Directory
3. Contacting third-party SOC team to investigate further

After completing all these actions, we recieved a response from the SOC team and they said there were no indication of someone changing the `userPrincipalName` of the account at that time.

## Windows Events

What is great about Cortex XDR is that all events from endpoints and servers are logged which enables us to filter all events by the event ids. Here are overview of Windows Event IDs that are useful in a investigation:

* [Event ID 4625 - A failed login attempt occurred](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4625)
* [Event ID 4726 - A user account was deleted](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4726)
* [Event ID 4738 - A user account was updated](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4738)
* [Event ID 4743 - A computer account was deleted](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4743)

A great website for finding more Event IDs are [UltimateWindowsSecurity](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/default.aspx?i=j) where all the events are described by details which I highly recommend checking out.

## Ingvestigation

In Cortex XDR we can go to Panel -> Panel -> Panel.

We can then enter the 4738 to see who performed the update.

We can see if there were any failed login attempts and sign-in attempts using 4625 event id.

## Conclusion


