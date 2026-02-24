---
title: "Deploying Dynamic 365 to Intune (Android)"
created: 2026-02-29
modified: 2026-02-29
tags: ["INTUNE"]
draft: true
---

## Introduction

We recently choose to migrate from Salesforce over to Microsoft Dynamics 365 Sales. The Sales Team has requested me to roll out the Dynamics 365 Sales application on Android so they can login and view the data from there. In this post I'll go through setting up Dynamics 365 Sales application on Intune.

## Deployment

1. Go to **Apps -> Android**.
    
    ![[0038 Deploying-Dynamic-365-to-Intune-(Android)-01.png]]

2. Click on **Create** 
    
    ![[0038 Deploying-Dynamic-365-to-Intune-(Android)-02.png]]

3. Select **Android Store App**

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android)-03.png]]

4. Find **Dynamic 365 Sales** on Enterprise Store and click on **Select**.

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android)-04.png]]

5. Wait 5 minutes... And the app should show up on **Android Apps** section.

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android)-05.png]]

6. Go to **Dynamic 365 Sales -> Properties** and configure who should have access to the app.

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android)-06.png]]


## Conclusion

