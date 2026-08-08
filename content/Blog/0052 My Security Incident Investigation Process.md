---
title: "My Security Incident Investigation Process"
created: 2026-08-07
modified: 2026-08-07
tags: ["Personal"]
draft: true
---

## Introduction

So basically, I recently have been investigating important security incidents. I thought about writing the way I think and investigate these secuirty incidents since I realized that many engineers don't **"Think outside of the Box"** which leads to them missing crucial informations. Hopefully, this article can provide some real value to someone who needs it.

## Telemetry Data

**You cannot protect something you don't know anything about.** I understand that many organizations doesn't want to spend money on collecting telemetry data from their endpoints, servers, and cloud resources. It's crucial to collect telemetry data since when a security incidnet occurs where company data is compromised, the source of truth will be the telemetry data. **It might sound expensive to spend \$400/m on storing telemetry data but the real expense comes when the company is compromsied and there is no telemetry data to follow.**

**Learn the tools in your toolbox.** As a cybersecurity professional it's crucial for you to understand the tools inside of your toolbox. As an example if your organization is using Microsoft Sentinel it's important to learn KQL since that can help you significantly during a investigation and save you hours and hours. 

## Filtering Telemetry Data

**We humans are not meant to look through telemetry data.** I understand that there are some people who does love to look through telemetry data but the point of telemetry data is to retrieve the data that is important for us. And using that telemetry data to visualize it in a pattern to understand the security incident further. As an example a employee usually logins from `49.110.112.10` but one day logins from `45.33.110.15` and then starts performing a port scan.

**Most common instinct in that situation is to focus on IPv4 address `45.33.110.15`**. Instead we should investigate other IPv4 addresses that has logged into the account and then investigate all the IPv4 addresses. If this happened inside the internal network it's worth looking through VPN logs and see the connections it tried to perform in the network.

## Offensive Security

**Learn offensive security to understand threat actors.** I believe it's crucial for us as cybersecurity professionals to learn the tools and techniques threat actors uses to compromise organizations. As an example when a threat actor obtains access to our internal network it's common knowledge that they will perform ping scan, port scans, smb enumerations, and try to abuse weak credentials. **However, if you have played HackTheBox you know there are many other things they can abuse within the active directory such as kerberoasting, ldap, and as-rep roasting.**

## Affected Resources

**Communication is extremely important.** Once all the information is collected we should communicate with the owners of the assets that where targeted by the threat actor to understand the data it holds. This will help us with understanding what they could do with the data such as gaining access again  or damage the reputation of the company.

## Canvas

**Make the telemetry data and other information readable.** I would recommend creating a canvas inside of obsidian note taking app and store relevant informations such as username, hostname, IPv4 addresses, and the KQL used to retrieve these data. It will help with visualizing the telemetry data and also provide a quick reference to the KQL to use to retrieve that data. I'll try to attach a canvas from a security incident I investigated a while ago which might help with understanding the importance of canvas.

## Conclusion

I mainly focus on Offensive Security and it's interesting to see the knowledge I learnt come in good use because I'm able to recognize malicious patterns that even our SOC vendor couldn't recognize. I really believe it's important for us as cybersecurity professionals to learn about offensive security since that will allow us to better visualize the attack patterns. I also believe using tools like obsidian is incredibly important since it allows us to visualize complex data into something readable and understandable *(I'm not sponsored by obsidian, I just love the app too much)*.