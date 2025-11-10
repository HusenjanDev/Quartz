---
title: "Automating Email Sorting with Power Automate"
created: 2025-11-10
modified: 2025-11-10
description: "Power Automate comes with \"When I receive Email\" feature which enables us to perform actions on all new incoming emails."
keywords: ["Power Automate", "Automating Email Sorting"]
tags: ["PA"]
draft: false
---

## Information

We are currently implementing a new system into organization and the employees using these systems made a request to implement automatic email sorting where all emails with PDFs are moved to a different folder than emails without PDF documents.

## Automating with Power Automate

![[0000 Automating-Email-Sorting-with-Power-Automate.png]]

The **"When I receive email"** feature in Power Automate allows us to do the following: 

* When an email comes into mailbox a boolean variable is created with name `hasPDF` and the value is `false`. 
* The while loop will enumerate through all the attachment and when PDF document is found the `hasPDF` variable is set to `true`. 
* The last condition checks if `hasPDF` variable is `true` and then moves the email to `Processed by Power Automate` folder. 

With that implementation each time a email comes into the mailbox it will automatically move the emails with PDF attached to them into a different folder. The emails without PDFs will still be inside of default inbox folder.

## Conclusion

Power Automate is a tool designed to automate manual tasks which employees performs. In this situation it came in good use because all emails with PDF attachments will be moved to different folder than the emails without PDF documents. This allows employees to focus on emails without PDFs attached to them.
