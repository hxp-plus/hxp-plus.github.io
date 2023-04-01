---
layout: post
title: "How to disable the vCenter Server Update Notification banner"
author: "Xiping Hu"
header-style: text
tags:
  - VMware
---
Currently, the only way I am aware of for disabling this notification is to actually disable the vCenter Server Life-Cycle Manager Remote Plugin itself. You can do this by navigating to Administration->Solutions->Client Plugins and then selecting "**vCenter Server Life-cycle Manager**" and click on the Disable button. You can refresh the webpage or logout and you should no longer see the notification banner.

# References
<https://williamlam.com/2021/03/quick-tip-how-to-disable-the-vcenter-server-update-notification-banner.html>