---
layout: post
title: "Gentoo Linux Installation Part 2: Install KDE Desktop Based on CLI Installation"
author: "Xiping Hu"
header-style: text
tags:
- Gentoo
- Linux
---

## Install sudo

```bash
emerge sudo
echo "%wheel ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
usermod -aG wheel hxp
```

## Add USE flag

```bash
echo "USE=\"bindist mmx sse sse2 mmxext dbus udev branding icu python X acpi display-manager sddm gtk handbook libkms wallpapers pulseaudio legacy-systray gtk2 gtk3 -gtk -gnome\"" >> /etc/portage/make.conf
echo "INPUT_DEVICES=\"evdev keyboard mouse synaptics\"" >> /etc/portage/make.conf
```

## Choose Profile

```bash
eselect profile list
```

```text
Available profile symlink targets:
  [1]   default/linux/amd64/17.1 (stable) *
  [2]   default/linux/amd64/17.1/selinux (stable)
  [3]   default/linux/amd64/17.1/hardened (stable)
  [4]   default/linux/amd64/17.1/hardened/selinux (stable)
  [5]   default/linux/amd64/17.1/desktop (stable)
  [6]   default/linux/amd64/17.1/desktop/gnome (stable)
  [7]   default/linux/amd64/17.1/desktop/gnome/systemd (stable)
  [8]   default/linux/amd64/17.1/desktop/gnome/systemd/merged-usr (dev)
  [9]   default/linux/amd64/17.1/desktop/plasma (stable)
  [10]  default/linux/amd64/17.1/desktop/plasma/systemd (stable)
  [11]  default/linux/amd64/17.1/desktop/plasma/systemd/merged-usr (dev)
  [12]  default/linux/amd64/17.1/desktop/systemd (stable)
  [13]  default/linux/amd64/17.1/desktop/systemd/merged-usr (dev)
  [14]  default/linux/amd64/17.1/developer (exp)
  [15]  default/linux/amd64/17.1/no-multilib (stable)
  [16]  default/linux/amd64/17.1/no-multilib/hardened (stable)
  [17]  default/linux/amd64/17.1/no-multilib/hardened/selinux (stable)
  [18]  default/linux/amd64/17.1/no-multilib/systemd (dev)
  [19]  default/linux/amd64/17.1/no-multilib/systemd/merged-usr (dev)
  [20]  default/linux/amd64/17.1/no-multilib/systemd/selinux (exp)
  [21]  default/linux/amd64/17.1/systemd (stable)
  [22]  default/linux/amd64/17.1/systemd/merged-usr (dev)
  [23]  default/linux/amd64/17.1/systemd/selinux (exp)
  [24]  default/linux/amd64/17.1/clang (exp)
  [25]  default/linux/amd64/17.1/systemd/clang (exp)
  [26]  default/linux/amd64/17.0 (dev)
  [27]  default/linux/amd64/17.0/selinux (exp)
  [28]  default/linux/amd64/17.0/hardened (exp)
  [29]  default/linux/amd64/17.0/hardened/selinux (exp)
  [30]  default/linux/amd64/17.0/desktop (exp)
  [31]  default/linux/amd64/17.0/desktop/gnome (dev)
  [32]  default/linux/amd64/17.0/desktop/gnome/systemd (exp)
  [33]  default/linux/amd64/17.0/desktop/plasma (dev)
  [34]  default/linux/amd64/17.0/desktop/plasma/systemd (exp)
  [35]  default/linux/amd64/17.0/developer (exp)
  [36]  default/linux/amd64/17.0/no-multilib (exp)
  [37]  default/linux/amd64/17.0/no-multilib/hardened (exp)
  [38]  default/linux/amd64/17.0/no-multilib/hardened/selinux (exp)
  [39]  default/linux/amd64/17.0/systemd (dev)
  [40]  default/linux/amd64/17.0/x32 (dev)
  [41]  default/linux/amd64/17.0/musl (exp)
  [42]  default/linux/amd64/17.0/musl/clang (exp)
  [43]  default/linux/amd64/17.0/musl/hardened (exp)
  [44]  default/linux/amd64/17.0/musl/hardened/selinux (exp)
```

```bash
eselect profile set 9
```

## Install dbus

```bash
emerge --changed-use --deep @world
emerge -v sys-apps/dbus
/etc/init.d/dbus start
rc-update add dbus default
```

## Install Xorg Drivers

```bash
emerge -v x11-base/xorg-drivers
emerge -v x11-base/xorg-x11
```

## Allow users to video access

```bash
gpasswd -a root video
gpasswd -a hxp video
```

## Start installing KDE Plasma

```bash
echo "USE=\"minimal harfbuzz bindist mmx sse sse2 mmxext dbus udev branding icu python X acpi display-manager sddm gtk handbook libkms wallpapers pulseaudio legacy-systray gtk2 gtk3 -gtk -gnome\"" >> /etc/portage/make.conf
emerge --changed-use --deep @world
emerge -v kde-plasma/plasma-desktop
dispatch-conf
chmod +s /sbin/unix_chkpwd
emerge -v kde-plasma/kdeplasma-addons kde-apps/dolphin x11-misc/sddm kde-plasma/systemsettings kde-plasma/kscreen kde-apps/konsole
```

## Change display manager

```bash
emerge -v gui-libs/display-manager-init
rc-update add display-manager default
sed -i 's/DISPLAYMANAGER="xdm"/DISPLAYMANAGER="sddm"/' /etc/conf.d/display-manager
usermod -a -G video sddm
```

## Reboot and test KDE Installation

```bash
reboot
```

## Install basic KDE apps first

```bash
emerge -v kde-plasma/plasma-meta
emerge -v kde-plasma/kdeplasma-addons kde-apps/kwalletmanager kde-apps/dolphin x11-misc/sddm kde-plasma/systemsettings kde-plasma/kscreen kde-apps/konsole
emerge -v net-misc/networkmanager kde-plasma/plasma-nm
rc-update del dhcpcd default
rc-service NetworkManager start
rc-update add NetworkManager default
```

## Then install all KDE apps (optional)

```bash
echo 'USE="postproc harfbuzz mmx sse sse2 mmxext dbus udev branding icu python X acpi display-manager sddm gtk handbook libkms wallpapers pulseaudio legacy-systray gtk2 -gtk -gnome"' >> /etc/portage/make.conf
echo 'ABI_X86="(64)"' >> /etc/portage/make.conf
emerge -vuND --keep-going  @world --exclude="nodejs"
emerge -vuND --keep-going  @world --exclude="openssl http-parser"
emerge firefox kde-apps/kde-apps-meta
```

## Install a VNC server (optional)

Install TigerVNC Server

```bash
echo 'net-misc/tigervnc server' >> /etc/portage/package.use/tigervnc
emerge --update --newuse net-misc/tigervnc
```

Set VNC passwd for user hxp

```bash
su - hxp
vncpasswd
exit
```

Configure xstartup for user hxp

```bash
su - hxp
echo '#!/bin/sh
eval "$(dbus-launch --sh-syntax --exit-with-session)"
export LANG="en_US.utf8"
export LC_ALL="en_US.UTF-8"
export LC_TYPE="en_US.utf8"
startplasma-x11 &' >> ~/.vnc/xstartup
chmod +x ~/.vnc/xstartup
exit
```

Make VNC server start on system boot

```bash
echo DISPLAYS="hxp:1" >> /etc/conf.d/tigervnc
echo ":1=hxp ">> /etc/tigervnc/vncserver.users
rc-service tigervnc start
```

## Install a noVNC server (optional)

```bash
emerge -av novnc
```

Start the server by:

```bash
novnc --vnc localhost:5901
```
