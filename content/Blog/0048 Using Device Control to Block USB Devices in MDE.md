---
title: "Using Device Control to Block USB Devices in MDE"
created: 2026-07-03
modified: 2026-07-03
tags: ["Windows", "MDE", "USB"]
draft: true
---

## Introduction

I'm currently working on implementing security controls for USB devices in my organization to improve our security posture. Fortunately for us Microsoft Defender for Endpoint (MDE) comes with Device Control which allows us to manage read, write, and execute access to USB devices. In this article I'll go through the purpose of implementing Device Controls and then go through actually implementing it.

## Purpose of Managing USB Devices

Majority of people uses USB devices to store files, images, scripts, and in some situations cracked versions of paid applications. And who knows the employee might find a USB device at the doorstep of the office and curiosity might get ahead of them. When the employee runs a file from the USB device a terminal shows up for a milisecond and the cyber threat actors now has access to the internal network.

The whole purpose of managing USB devices is to prevent these kinds of situations to reduce our attack surface. Using Device Controls we can configure it so users can only perform specific operations such as read, write, and execute on USB devices. I would recommend analyzing your organization and assess the minimum amount of privileges the employees needs on USB devices to perform their day-to-day work.

## Reusable Settings

## Device Control

## Conclusion

