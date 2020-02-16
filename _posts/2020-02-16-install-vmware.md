---
layout: post
title: "Install VMware Workstation Pro on Arch Linux"
subtitle: ''
author: "hxp"
header-style: text
tags:
- WMware
---

To install WMware Workstation Pro on Arch Linux, DO NOT download the bundle file on the official website. I tired, but I suspect the installer is incompatible with Arch Linux.

use AUR instead

``` shell
yay -S --noconfirm --needed ncurses5-compat-libs
sudo pacman -S fuse2 gtkmm linux-headers pcsclite libcanberra
yay -S --noconfirm --needed  vmware-workstation
modprobe -a vmw_vmci vmmon
```
