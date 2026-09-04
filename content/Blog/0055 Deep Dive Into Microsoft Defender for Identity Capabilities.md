---
title: "Deep Dive Into Microsoft Defender for Identity Capabilities"
created: 2026-09-04
modified: 2026-09-04
tags: ["MDI"]
draft: false
---


## Introduction

Microsoft Defender for Identity is a product which was released by Microsoft to detect and respond to identity based attacks across on-premise, cloud, and hybrid cloud environments. It also comes with a identity sensor which can be installed on the domain controller to collect telemetry data which can be used for detecting and responding to identity based attacks. This is especially beneficial for organizations with multiple of domain controllers because of acquisitions and mergers. 

In this document I'll be focusing on the identity sensor and the capabilities the sensor comes with that can be beneficial for your organization.

## Core Capabilities Of Identity Sensor

The core capability of Microsoft Defender for Identity sensor is to inspect and monitor the traffics which happens on the domain controller. Which will help with detecting and responding to **Reconnaissance**, **Compromised Credentials**, **Lateral Movement**, and **AD Domain Dominance** attacks. Here is a quick summary of all these attacking methods.

| Attack Method           | Description                                                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Reconnaissance          | Once a threat actor obtains initial access the first thing they will do is to enumerate through the LDAP or SMB to obtain valid usernames.                                                           |
| Compromised Credentials | Once the threat actors obtains valid usernames they will perform bruteforce attacks or password spray attacks to obtain initial access to accounts with weak credentials including service accounts. |
| Lateral Movement        | At this stage the threat actors will try to dump the LSASS to obtain sensitive credentials or the password hashes of the service accounts to crack the password.                                     |
| AD Dominance            | Once a threat actor obtains enough privileges the next step is usually to create golden ticket, DCShadow, and Domain Controller Replication to establish                                             |

It's core functionality is meant to detect and respond to these complex attack techniques which are commonly used by threat actors. Additionally, since the identity sensor is installed on the domain controller it will analyze the group policies and come with recommendations to harden our environment which can help with reducing the attack surface.

## Overview of Accounts

![[0055 Deep Dive Into Microsoft Defender for Identity Capabilities 01.png]]

The identity sensor also allows us to see all the normal accounts and service accounts that are setup on the domain controller. And since the identity sensor runs as a administrator on the domain controller it allows us to disable and force password change on accounts in the domain controller.

## Password Policies

![[0055 Deep Dive Into Microsoft Defender for Identity Capabilities 02.png]]

In a enterprise there can be hundreds of domain controllers because of mergers and acquisitions. And it can be quite complex to keep track of all the different group policies related to passwords. However, with Microsoft Defender for Identity (MDI) we can quickly find the password policies assigned to these different domain controllers to ensure there is a unified password policies throughout the organization.

## Security Recommendation

![[0055 Deep Dive Into Microsoft Defender for Identity Capabilities 03.png]]

The identity sensor also analyzes the domain controller and comes with security recommendations to improve the security posture of it. It will analyze everything from network protocol misconfigurations to dormant accounts with high privileges which hasn't been used for months to years. 

## Architectural Benefits with Identity Sensor

<image style="display:flex; width: 75%;margin:auto;" src="0055 Deep Dive Into Microsoft Defender for Identity Capabilities 04.svg"/>

In large enterprise with hybrid environments the Microsoft Entra ID Sync is usually configured to only perform a one-way sync where the domain controller is the point of trust. This can prevent SOC vendors from responding to security incidents that happens on-premise since Microsoft Entra ID doesn't write back to on-premise domain controller. 

With Microsoft Defender for Identity this changes because the identity sensor runs as administrator on the domain controller and comes with capabilities to disable and force password change on accounts which is directly done through domain controller. If you're running a SIEM you can read more about using APIs that comes with Microsoft Defender for Identity here [Microsoft Defender for Identity API](https://learn.microsoft.com/en-us/graph/api/security-identityaccounts-invokeaction?view=graph-rest-1.0&tabs=http).

This is a huge benefit for us who doesn't want to grant the SOC vendor direct access to the master domain controller where the SOC analyst could mistakenly do something which could damage the production or create disturbance throughout the business.

## Conclusion

Microsoft Defender for Identity is an excellent tool for organizations to protect themselves from identity based attacks. The game changer is the identity sensor since it comes with features which allows us to obtain a clear overview of all the domain controllers, misconfigurations, accounts, service accounts, dormant accounts with high privileges, and much more. It also allows us to provide our SOC vendor with necessary access to respond to security incidents that happens in our on-premise active directory without providing them access to the master domain controller. I would recommend large organizations with multiple of domain controllers to use Microsoft Defender for Identity.

## Sources

- [Why Microsoft Defender for Identity](https://www.quorumcyber.com/insights/why-microsoft-defender-for-identity/)
- [Microsoft Defender for Identity Overview](https://learn.microsoft.com/en-us/defender-for-identity/what-is)

