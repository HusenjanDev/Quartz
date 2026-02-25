---
title: "Deploying Dynamic 365 to Intune (Android & iOS)"
created: 2026-02-29
modified: 2026-02-29
tags: ["INTUNE"]
draft: true
---

## Introduction

We recently choose to migrate from Salesforce over to Microsoft Dynamics 365 Sales. The Sales Team has requested me to roll out the Dynamics 365 Sales application on Android so they can login and view the data from there. In this post I'll go through setting up Dynamics 365 Sales application on Intune.

## Android Deployment

1. Go to **Apps -> Android**.
    
    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-01.png]]

2. Click on **Create** 
    
    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-02.png]]

3. Select **Android Store App**

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-03.png]]

4. Find **Dynamic 365 Sales** on Enterprise Store and click on **Select**.

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-04.png]]

5. Wait 5 minutes... And the app should show up on **Android Apps** section.

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-05.png]]

6. Go to **Dynamic 365 Sales -> Properties** and configure who should have access to the app.

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-06.png]]


## iOS Deployment

1. Go to **Apps -> iOS/iPadOS**

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-07.png]]

2. Click on **Create**

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-08.png]]

3. Select **iOS Store App**

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-09.png]]

4. Click on  **Search the App Store**

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-10.png]]

5. Search up **Dynamics 365 Sales** and select the app

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-11.png]]

6. Enter and review the fields **Name, Description, Publisher, Applicable device types, and other informations**

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-12.png]]

7. Select your Scope Tag

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-13.png]]

8. Select the users/devices that should have access to the app

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-14.png]]

9. Review the configuration and click on **Create**

    ![[0038 Deploying-Dynamic-365-to-Intune-(Android-and-iOS)-15.png]]

## Conclusion

