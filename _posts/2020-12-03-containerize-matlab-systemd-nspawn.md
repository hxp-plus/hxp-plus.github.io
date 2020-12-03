---
layout: post
title: "Containerize MATLAB with systemd-nspawn"
subtitle: 'Install MATLAB in Debian container inside Arch Linux to avoid compatibility bugs'
author: "hxp"
header-style: text
tags:
- MATLAB
- Container
- Linux
---

## Set up Debian 9 environment

Change directory

```bash
sudo su
cd /var/lib/machines
```

Install `debootstrap` and `debian-archive-keyring`

```bash
sudo pacman -S debootstrap debian-archive-keyring
```

Use `debootstrap` to set up a `debian` environment

```bash
debootstrap --include=systemd-container --components=main,universe stretch matlab-debian9 https://mirrors.163.com/debian
```

Set password for Debian

```bash
systemd-nspawn -D ./matlab-debian9/
passwd
```

## Install Firefox and run

Edit `/etc/apt/sources.list`

```bash
systemd-nspawn -D ./matlab-debian9
deb https://mirrors.163.com/debian stretch main contrib non-free
```

Install `Firefox`

```bash
apt install firefox-esr
```

Exit the container

```bash
exit
```

And enable `xhost`

```bash
xhost +local:
```

Then run `Firefox` inside container

```bash
systemd-nspawn --setenv=DISPLAY=:0 \
               --setenv=XAUTHORITY=~/.Xauthority \
               --bind-ro=$HOME/.Xauthority:/root/.Xauthority \
               --bind=/tmp/.X11-unix \
               -D /var/lib/machines/matlab-debian9 firefox
```

## Install MATLAB

mount the MATLAB iso

```bash
sudo mkdir /mnt/matlab
sudo mount -t iso9660 'Matlab98R2020a_Linux 64.iso' /mnt/matlab
```

boot into the container

```bash
sudo systemd-nspawn -b -D /var/lib/machines/matlab-debian9
```

install requisite libraries

```bash
apt-get install xorg build-essential libgtk2.0-0 libnss3 libasound2
```

set a lower limit of number of open files by

```bash
ulimit -n 100000
```

and then change directory into `/mnt/matlab` and install MATLAB

then install and run MATLAB

```bash
/usr/local/Polyspace/R2020a/bin/matlab
```

