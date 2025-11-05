---
title: "Implementing Microsoft Defender SmartScreen"
created: 2025-11-05
modified: 2025-11-05
description: "Microsoft Defender SmartScreen is designed to protect endpoints from phishing sites, malwares, and ransomwares by notifying the user before execution."
keywords: ["Microsoft Defender XDR", "Microsoft Defender for Endpoint"]
tags: ["Microsoft Defender XDR", "SmartScreen"]
draft: false
---

## Information

Microsoft Defender SmartScreen is desinged to protect endpoints from malicious websites, applications, and files by displaying an early warning. It comes with anti-phishing, anti-malware, reputation-based url, and app protection to detect malicious applications.

## Implementation

1. Go to **Microsoft Intune -> Endpoint Protection -> Attack Surface Reduction**.
    ![[0000 Implementing-Microsoft-Defender-SmartScreen-00.png]]

2. Enter **Name and Description**.
    ![[0000 Implementing-Microsoft-Defender-SmartScreen-02.png]]

3. Select **Scope Tags**.
    ![[0000 Implementing-Microsoft-Defender-SmartScreen-03.png]]

4. Select **All Devices**.
    ![[0000 Implementing-Microsoft-Defender-SmartScreen-04.png]]

5. Review **Configuration** and click on **Create**.
    ![[0000 Implementing-Microsoft-Defender-SmartScreen-05.png]]

    > [!IMPORTANT]+ IMPORTANT
    > Once the Application Control Policy is applied on the endpoints it will force the users to reboot within 10 minutes. If your company has change management system, I highly recommend creating a change request for the following implementation.

## Conclusion

