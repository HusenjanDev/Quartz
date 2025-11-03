---
title: "Using Watchlist in Microsoft Sentinel"
created: 2025-11-3
modified: 2025-11-3
description: "Watchlists in Microsoft Sentinel is desinged to help security analysts with quickly correlate data by getting values from a list."
keywords: ["MDE", "MDE-XDR"]
tags: ["MDE", "MDE-XDR", "KQL"]
draft: false
---

## Information

Watchlists in Microsoft Sentinel is commonly used for storing a list of data which can be retrieved by security analysts to correlate data. For example, you can create a list of executives, termninated employees, and service accounts and retrieve information such as UserPrincipalName, Business Email, and etc...

## Creating Watchlist

1. Go to Microsoft Sentinel -> Watchlist.
    ![[0000 Using-Watchlist-in-Microsoft-Sentinel-00.png]]

2. Click on New.
    ![[0000 Using-Watchlist-in-Microsoft-Sentinel-01.png]]

3. Enter Name, Description, and Alias.
    ![[0000 Using-Watchlist-in-Microsoft-Sentinel-02.png]]

4. Upload a `dat.tsv` file which consists a list of all information.
    ![[0000 Using-Watchlist-in-Microsoft-Sentinel-03.png]]

5. Review and create watchlist.
    ![[0000 Using-Watchlist-in-Microsoft-Sentinel-04.png]]

## Using Watchlist

Once the watchlist is created, we can retrieve data using `_GetWatchlist()` operator with `project` to return specific values.

![[0000 Using-Watchlist-in-Microsoft-Sentinel-05.png]]

```kql title="Proof of Concept"
let cEventID = dynamic([4625]);
let iAccounts = dynamic(["NT AUTHORITY\\ANONYMOUS LOGON", "MSOL"]);
let domainAdmins = (_GetWatchlist("VIPUsers") | project UserPrincipalName);
SecurityEvent

| where TimeGenerated > ago(1d)
| where EventID in (cEventID)
| where not(Account has_any (iAccounts))
| where TargetUserName in (domainAdmins)
| summarize count() by TargetUserName
| render barchart 
```

## Conclusion

Watchlists are great for retrieving values which are frequently used as that allows us to quickly collerate data. It's recommended to setup watchlists for executive and domain administrator users as that will enable us to quickly investigate a incident on these accounts once it occurs.
