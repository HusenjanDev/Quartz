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

We are currently implementing a new system into organization and the employees using these systems made a request to implement automatic email sorting where all emails with PDFs are moved to a different folder than emails without PDF documents. Unfortunately, this is not possible with default Outlook email rules therefore we have to automate it through Power Automate.

## Automating with Power Automate

1. Go to **Power Automate -> Connectors -> Office 365 Outlook**.
    
    ![[0000 Automating-Email-Sorting-with-Power-Automate-00.png]]

2. Click on **When I receive new email arrives**

    ![[0000 Automating-Email-Sorting-with-Power-Automate-01.png]]

3. Setup connector on a account.

    ![[0000 Automating-Email-Sorting-with-Power-Automate-02.png]]

4. Initalize a variable with the name `hasPDF` with value `false`.

    ![[0000 Automating-Email-Sorting-with-Power-Automate-03.png]]

5. Create **For Each** and include **Attachment** from Outlook connector.

    ![[0000 Automating-Email-Sorting-with-Power-Automate-04.png]]

6. Inside **Condition** use the parameters **Has Attachments** and **Name** with the following configuration.

    ![[0000 Automating-Email-Sorting-with-Power-Automate-05.png]]

7. If PDF attachment is included in email, set `hasPDF` variable to `true`.

    ![[0000 Automating-Email-Sorting-with-Power-Automate-06.png]]

8. Inside condition configure it to check if `hasPDF` is `true`.
    ![[0000 Automating-Email-Sorting-with-Power-Automate-07.png]]

9. When `hasPDF` is `true` create another object to move the email to different folder by using **Message Id**.

    ![[0000 Automating-Email-Sorting-with-Power-Automate-08.png]]

Short summary of what happens when we receive a new email to the connected account is the following.

* When an email comes into mailbox a boolean variable is created with name `hasPDF` and the value is `false`. 
* The while loop will enumerate through all the attachment and when PDF document is found the `hasPDF` variable is set to `true`. 
* The last condition checks if `hasPDF` variable is `true` and then moves the email to `Processed by Power Automate` folder. 

With that implementation each time a email comes into the mailbox it will automatically move the emails with PDF attached to them into a different folder.

## Conclusion

Power Automate is a tool designed to automate manual tasks which employees performs. In this situation it came in good use because all emails with PDF attachments will be moved to different folder than the emails without PDF documents. This allows employees to focus on emails without PDFs attached to them.
