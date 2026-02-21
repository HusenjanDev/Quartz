---
title: "Sorting Event Viewer Logs using Python-EVTX (Part 1)"
created: 2026-02-29
modified: 2026-02-29
tags: ["PYTHON", "EVTX", "WINDOWS"]
draft: true
---

## Introduction

In Windows the Event Viewer Logs are a gold mine since it contains informations about login attempts, user account updates,  computer account updates, and much more. Intead of viewing these events manually we can instead use Python-EVTX library. In this article I'll go through implementing a python script which reads through Event Viewer Logs using the python-evtx library.

## Event Viewer Logs

![[0037 Sorting-Event-Viewer-Logs-using-Python-EVTX-01.png]]

All events in Windows is assigned a `EventID` which identifies the action that the user performed on the system. An example the `EventID: 4625` means a failed login attempt occurred on our environment. The [Windows Security Log Events](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/) is a gold mine for finding all the event ids that exists in Windows.

## Sorting Events

I'll be using the event viewer sample data from [DeepBlueCLI](https://github.com/sans-blue-team/DeepBlueCLI) to detect total failed login that occurred on a account and assign it a risk score. You can use real events from your domain controller but in my case I'm writing a blog therefore I'll be using public sample data.

```python
import argparse
from enum import Enum
import Evtx.Evtx as evtx
import xml.etree.ElementTree as et

# XML namespace for Windows Events
ns = {"ns": "http://schemas.microsoft.com/win/2004/08/events/event"}

def risk_evaluation(logs):
    # Converting logs to a dict object to count total failed logon attempts
    dlogs = {i:logs.count(i) for i in logs}
    
    # Checking the danger for username
    for (key, value) in dlogs.items():
        if value > 10:
            print(f"[#] Suspicious Activities (RISK HIGH)")
            print(f"[*] Username: {key}")
            print(f"[*] Total Failed Logon: {value}\n")
        elif value > 5:
            print(f"[#] Suspicious Activities (RISK MEDIUM)")
            print(f"[*] Username: {key}")
            print(f"[*] Total Failed Logon: {value}\n")
        else:
            print(f"[#] Suspicious Activities (RISK LOW)")
            print(f"[*] Username: {key}")
            print(f"[*] Total Failed Logon: {value}\n")

def main():
    # Script parameters
    parser = argparse.ArgumentParser()
    parser.add_argument("evtx", help="Enter the EVTX file location", type=str)
    parser.add_argument("eventid", help="Enter the event id to use for filtering", type=str)
    parser.add_argument("--username", default="", help="Enter the username for filtering", type=str)
    args = parser.parse_args()

    # mlogs: used when --username parameter is empty 
    mlogs = []
    # slogs: used when --username paramter is in use
    slogs = []

    # Opening Evtx File
    with evtx.Evtx(args.evtx) as log:
        # Going through records inside EVTX file
        for event in log.records():
            # Parsing the XML data from EVTX file
            root = et.fromstring(event.xml())
            # Getting EventID from events
            event_id = root.findtext(".//ns:EventID", namespaces=ns)

            # Checking if EventID matches user input EventID
            if event_id == args.eventid:
                # Getting the Data field from event
                for data in root.findall(".//ns:Data", namespaces=ns):
                    name = data.get("Name")
                    
                    if name == "TargetUserName":
                        # Field is used when --username parameter is in use
                        if data.text == args.username and len(args.username) > 0:
                            slogs.append(data.text)
                        else:
                            mlogs.append(data.text)

    # Calling risk_evaluation function
    if len(slogs) > 0:
        risk_evaluation(slogs)
    else:
        risk_evaluation(mlogs)

main()
```

```plaintext title="Terminal"
> python .\main2.py --evtx events/smb-password-guessing-security.evtx --eid 4625
[#] Suspicious Activities (RISK LOW)
[*] Username: JcDfcZTc
[*] Total Failed Logon: 1

[#] Suspicious Activities (RISK HIGH)
[*] Username: Administrator
[*] Total Failed Logon: 3560
```

I also built a `--username` parameter which returns total failed logon attempt that occurred on a specific account instead of all accounts in out environment.

## Conclusion

A-lot of security engineers uses the Event Viewer to investigate a security incident but that is so 2010. Instead we should become familiar with python libraries such as `python-evtx` to quickly craft a python script which can detect threats in our environment. Hopefully, this article has helped you with understanding `python-evtx` library and the purpose of it.