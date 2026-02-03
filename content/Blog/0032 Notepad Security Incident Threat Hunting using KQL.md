---
title: "Notepad++ Security Incident Threat Hunting using KQL"
created: 2026-02-02
modified: 2026-02-02
tags: ["SIEM", "KQL"]
draft: false
---

## Introduction

Notepad++ infastructure was recently compromised by state sponsored hackers where they rediected the update traffic from notepad-plus-plus.org to an malicious site which contains a malicious executable program. According to Rapid7 the executable program responsible for updating notepad++ `gup.exe` executed a suspicious process `update.exe` which was downloaded from `95.179.213.0`.

## Threat Hunting

Using Azure Sentinel we can craft custom queries to investigate if any of our endpoints were affected by the Notepad++ security incident. This KQL query detects if any of our endpoints made connection to unauthorized URLs.

```sql title="DeviceNetworkEvents"
let AllowedUrls = dynamic(["notepad-plus-plus.org", "github.com", "release-assets.githubusercontent.com", "raw.githubusercontent.com"]);
DeviceNetworkEvents
| where TimeGenerated > ago(360d)
| where InitiatingProcessCommandLine startswith '"gup.exe"'
| where not(RemoteUrl has_any (AllowedUrls))
| where RemoteIPType <> "Loopback"
| where isnotempty(RemoteIP)
```

This KQL query allows us to identify all the executables, dynamic link libraries, and other files the `gup.exe` made in our endpoints.

```sql
DeviceFileEvents
| where TimeGenerated > ago(360d)
| where InitiatingProcessFileName startswith "gup.exe"
```

The [Rapid7 Team](https://www.rapid7.com/blog/post/tr-chrysalis-backdoor-dive-into-lotus-blossoms-toolkit/) researched the Notepad++ Security Incident and provided some IoC which we can use to detect if any of our endpoints were affected. 
```sql title="DeviceNetworkEvents"
let ioc = dynamic(["124.222.137.114", "59.110.7.32", "61.4.102.97", "95.179.213.0"]);
DeviceNetworkEvents
| where TimeGenerated > ago(360d)
| where RemoteIP has_any (ioc)
```

```sql title="DeviceFileEvents"
let ioc = dynamic(["a511be5164dc1122fb5a7daa3eef9467e43d8458425b15a640235796006590c9", "8ea8b83645fba6e23d48075a0d3fc73ad2ba515b4536710cda4f1f232718f53e", "2da00de67720f5f13b17e9d985fe70f10f153da60c9ab1086fe58f069a156924", "77bfea78def679aa1117f569a35e8fd1542df21f7e00e27f192c907e61d63a2e", "3bdc4c0637591533f1d4198a72a33426c01f69bd2e15ceee547866f65e26b7ad", "9276594e73cda1c69b7d265b3f08dc8fa84bf2d6599086b9acc0bb3745146600", "f4d829739f2d6ba7e3ede83dad428a0ced1a703ec582fc73a4eee3df3704629a", "4a52570eeaf9d27722377865df312e295a7a23c3b6eb991944c2ecd707cc9906", "831e1ea13a1bd405f5bda2b9d8f2265f7b1db6c668dd2165ccc8a9c4c15ea7dd", "0a9b8df968df41920b6ff07785cbfebe8bda29e6b512c94a3b2a83d10014d2fd", "4c2ea8193f4a5db63b897a2d3ce127cc5d89687f380b97a1d91e0c8db542e4f8", "e7cd605568c38bd6e0aba31045e1633205d0598c607a855e2e1bca4cca1c6eda", "078a9e5c6c787e5532a7e728720cbafee9021bfec4a30e3c2be110748d7c43c5", "b4169a831292e245ebdffedd5820584d73b129411546e7d3eccf4663d5fc5be3", "7add554a98d3a99b319f2127688356c1283ed073a084805f14e33b4f6a6126fd", "fcc2765305bcd213b7558025b2039df2265c3e0b6401e4833123c461df2de51a"]);
DeviceFileEvents
| where TimeGenerated > ago(360d)
| where SHA256 has_any (ioc)
```

If no endpoints in your organization made connections to these URLs or IPv4 addresses, and the file hash doesn't exist in your environment, it's safe to assume your endpoints were not affected by the incident.

## Mitigation

I would recommend updating the Notepad++ application profile on Microsoft Intune with the latest version as that comes with improved security controls for updates.

## Conclusion

Notepad++ is an application millions of people uses and unfortunately their infrastructure was compromised which lead to some endpoints being compromised. All we can do now is to investigate our environment and see if any endpoints where affected by the security incident.