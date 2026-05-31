---
title: "Issuing Internal SSL Certificates from AD CS"
created: 2026-04-20
modified: 2026-04-20
tags: ["SSL", "ADCS"]
draft: false
---

## Introduction

In our organization we have many internal web applications which uses the certificate provided by our Active Directory Certificate Services (AD CS). The information about internal certificates where lost because the colleague who was responsible for these requests resigned from the organization.

None of my colleagues took the ticket for a long time therefore I decided to take it and resolve it. I issued multiple of certificates but received the following error message `ERR_CERT_COMMON_NAME_INVALID` but after many failures I found out the solution and decided to share it here. 

## What was the issue?

The reason the internal certificate was throwing the error message `ERR_CERT_COMMON_NAME_INVALID` is because the **Common Name** and **DNS** had to contain the domain name. And I had only applied the domain name to DNS and not Common Name... 

## Issuing SSL Certificate

1. Connect to AD CS Server.
2. Open the `certlm.msc` application.
    <image style="display:flex; width: 80%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-01.png"/>

3. Inside `certlm.msc` open the folders **Certificates (Local Computer) -> Personal -> Certificates** and click on **All Tasks -> Request New Certificate**.
    <image style="display:flex; width: 80%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-02.png"/>

4. On **Certificate Enrollment Window** click on **Next**.
    <image style="display:flex; width: 80%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-03.png"/>

5. Click on **Next** again.
    <image style="display:flex; width: 80%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-04.png"/>

6. Select the **Active Directory Enrollment Policy**.
    <image style="display:flex; width: 80%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-05.png"/>

7. Inside the **Certificate Properties** configure the **Common Name** and **DNS** to be internal domain.
    <image style="display:flex; width: 45%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-06.png"/>

8. Click on **Enroll**.
    <image style="display:flex; width: 80%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-07.png"/>

## Exporting SSL Certificate

1. Open `certlm.msc` application.

2. Go to **Certificates - Local Computer -> Personal -> Certificates**.
    <image style="display:flex; width: 80%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-08.png"/>

3. Click on **All Tasks -> Export**.
    <image style="display:flex; width: 80%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-09.png"/>

4. On **Certificate Export Wizard** click on **Next**.
    <image style="display:flex; width: 45%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-10.png"/>

5. On **Export Private Key** select **Yes, export the private key**.
    <image style="display:flex; width: 45%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-11.png"/>

6. Use default export settings and click on **Next**.
    <image style="display:flex; width: 45%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-12.png"/>

7. Click on **Password** and enter a secure password and click on **Next**.
    <image style="display:flex; width: 45%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-13.png"/>

8. Choose a filename and export location.
    <image style="display:flex; width: 45%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-14.png"/>

9. Click on **Finish**.
    <image style="display:flex; width: 45%;" src="0041 Issuing-Internal-SSL-Certificates-from-AD-CS-15.png"/>

## Conclusion

While generating a certificate the most important part is to ensure the **Common Name** and **DNS** is the internal domain name otherwise the `ERR_CERT_COMMON_NAME_INVALID` error message will be thrown. Hopefully, someone with similar issue will solve their issues with this article.
