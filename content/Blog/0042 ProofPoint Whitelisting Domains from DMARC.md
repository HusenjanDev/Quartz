---
title: "ProofPoint Whitelisting Domains from DMARC"
created: 2026-04-29
modified: 2026-04-29
tags: ["Proofpoint"]
draft: true
---

## Introduction

We recently experienced a issue where an client was receiving DMARC error when sending emails to people in our organization. While they are figuring out the issue with their DMARC configurations we decided to whitelist the domain on our side. In this article I'll be going through whitelisitng a domain from DMARC in ProofPoint to hopefully help others whom needs it.

## Policy Routes

1. Go to [ProofPoint Email Protection](https://admin.proofpoint.com)

2. Open **Mail Flow** and click on **Policy Routes**.
    ![[0042 ProofPoint-Whitelisting-Domains-from-DMARC-01.png]]

3. Click on **New Policy**.
    ![[0042 ProofPoint-Whitelisting-Domains-from-DMARC-02.png]]

4. Enter **Policy Route Name** and **Description**.
    ![[0042 ProofPoint-Whitelisting-Domains-from-DMARC-03.png]]

5. Click on **Add Condition**.
    ![[0042 ProofPoint-Whitelisting-Domains-from-DMARC-04.png]]

6. Use the following configuration on the condition.
    * **Policy Route Condition**: Envelope Sender 
    * **Operator**: Contains
    * **Text**: @husenjan.com
    ![[0042 ProofPoint-Whitelisting-Domains-from-DMARC-05.png]]

7. Click on **Enable this Policy Route** and **Save**.
    ![[0042 ProofPoint-Whitelisting-Domains-from-DMARC-06.png]]

## DMARC Policy

1. Open **Email Protection** and click on **DMARC**.
    ![[0042 ProofPoint-Whitelisting-Domains-from-DMARC-07.png]]

2. Click on **General Settings**.
    ![[0042 ProofPoint-Whitelisting-Domains-from-DMARC-08.png]]

3. Click on **Edit**.
    ![[0042 ProofPoint-Whitelisting-Domains-from-DMARC-09.png]]

4. Scroll down to **Do not process on messages that belong to selected Policy Routes** and select the **Policy** and click **Save**.
    ![[0042 ProofPoint-Whitelisting-Domains-from-DMARC-10.png]]

## Conclusion

