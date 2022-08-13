---
layout: post
title: "Steps to Uninstall OneDrive Completely on Windows 10 Pro"
subtitle: ''
author: "Xiping Hu"
header-style: text
tags:
- Windows
- OneDrive
---

1. Open Group Policy Editor.
2. Go to `Computer Configuration` -> `Admimistrative Templates` -> `Windows Components` -> `OneDrive`.
3. Edit `Prevent the usage of OneDrive for file storage on Windows 8.1`, Change it to `Enabled`.
4. Reboot.
5. Go to `Settings` -> `Apps` -> `Apps & features` and Uninstall `Microsoft OneDrive`.
6. If your `Documents`, `Desktop` or `Pictures` folder is stored in `%HOMEPATH%\OneDrive`, right click `Desktop`, `Documents` and `Pictures`, and click `Properties`, then select `Location` tab, and click `Restore Default`, then `Apply`.
7. If some error happens, try opening Registry Editor and edit keys under `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders` and reboot.
