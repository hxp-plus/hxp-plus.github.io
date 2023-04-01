---
layout: post
title: "How to completely uninstall Ads on Windows 11"
author: "Xiping Hu"
header-style: text
tags:
  - Windows
---
# Completely uninstall Widgets feature on Windows 11

To uninstall the Windows 11 Widgets, open PowerShell (admin) and run the
```
Get-AppxPackage *WebExperience* | Remove-AppxPackage
```
command. 
# Remove ADs from Windows 11 search menu

Click on start the on settings
In left hand side click on privacy and security
Then on right hand side scroll down and click on search permissions
Scroll down again until you see "show search highlights"
Turn that option off and the images will disappear.

# Remove Chat from Windows 11

To remove Chat, open Group Policy Editor (Press Win+R, gpedit.msc, press Enter), go to Computer Configuration -> Administrative Templates -> Windows Components -> Chat, double click "Configure the Chat icon on the taskbar", click the Enabled radio button, and change "State" to "Disabled", then click "OK" and reboot.

# References
<https://pureinfotech.com/uninstall-widgets-powershell-windows-11/>

<https://answers.microsoft.com/en-us/windows/forum/all/how-do-i-remove-ads-from-windows-11-start-menu/acee8751-31e3-4abd-8caa-28ae7ba486fe>

<https://appuals.com/remove-chat-button-windows-11/>