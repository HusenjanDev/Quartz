---
title: "How to improve your SOC?"
created: 2026-02-16
modified: 2026-02-16
tags: ["SOC"]
draft: true
---

## Introduction

I understand that a-lot of organizations nowadays outsource their Security Operation Center (SOC) to a third party vendor because of compliance reasons. I also understand that a-lot of companies are frustrated with their SOC vendor since they are charging $15000/m to report 3 to 6 security incidents a month. In this article I will go through ways to get your money worth for the SOC vendor.

## Communication

It's easy to become frustrated with SOC vendor especially if they have provided a bad service. However, the past is the past and we have to focus on the present and start building a good relationships with the vendor and communicate with them as humans and not machines.

I would recommend scheduling a monthly meeting with the SOC vendor where you go through technical details about your environment. Additionally, I would recommend breaking the ice when the meeting starts by talking about things outside of work and build a relationships with them as that will help us much more in long term.

You can always take your money somewhere else but if you don't threat the vendor with respect they will provide the same quality of service as the previous vendor.

## SOC Service

When you have a meeting with the SOC vendor try understanding the service that you're paying for currently since there are a-lot of services that a SOC vendor provides such as monitoring firewalls, switches, endpoints, emails, and all events that comes from different connectors. Additionally, you will have to pay for them to respond to these incidents. There might be services that the previous security engineer forgot to purchase since they aren't familiar with our environment or don't understand what the other services provides.

## Understand Your Own Environment

A-lot of organizations are throwing SOC vendors into an black box and expecting them to figure out the environment by themselves from through thousands of logs. This method will never work since you will need to communicate with the SOC vendor and teach them up about our environment by telling them about users, applications, servers, and the infrastructure so they can craft better playbooks.

* **What is your organization's internal IP-addresses?**
* **What other identity management systems is the organization using?**
* **What business applications are critical?**
* **What endpoints are critical?**
* **How are the communication flow between the different servers?**
* **What are your users login patterns?**
* **Who should be allowed to apply changes to executives accounts?**

If you can provide all these informations to the SOC vendor they will be able to craft better playbooks using KQL to detect anomalous activities occurring in your environment. 

## Implement Processes

An SOC vendor can provide us with playbooks and custom detection rules to better secure our organization from threat actors. In order for them to respond accordingly they will need to know our environment and the process to follow when a specific security incident occurs.

An example when a malicious executable program is executed on a critical server should the SOC vendor isolate the system which could disturb the production and lose us millions or should they communicate with internal security engineers to plan for the isolation.

Another example when the CEO executes a malicious executable program should the SOC isolate the device and disable the user account or should they report to internal security engineers so we can communicate with the CEO and then isolate the PC and the account.


When there are no process for the SOC vendor they won't be able to respond accordingly to the security incident occurring in our environment. When there are uncertainty people will rather ignore the security incident as it never happened since they are scared of doing something wrong.

## Relationships

I remember for a long time ago a colleague of mine who dealt a-lot with oil companies told me the most important thing in this world is to build relationships. We should aim to build an relationship with SOC vendor as they are working for us instead of a service that we use this will significantly help us and them with responding to security incidents more effectively. 

## Conclusion

Instead of expecting the SOC vendor to figure out our environment, we should be teaching them about it so they can craft playbooks and custom detection rules for our environment so they can respond more effectively. Additionally, we should also provide them with processes to follow so there are no uncertainty when an security incident occurs since they won't be scared of doing something wrong.

Most important of it all remember to build strong relationship with the SOC vendor instead of threating them as a service that can be replaced at any point.