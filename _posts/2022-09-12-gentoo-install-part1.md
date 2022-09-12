---
layout: post
title: "Gentoo Linux Installation Part 1: Install Gentoo with CLI only"
author: "Xiping Hu"
header-style: text
tags:
- Gentoo
- Linux
---

## Getting Started

Boot the machine with a Linux LiveCD (a live cd with GUI is better) and connect to network.

## Partitioning the disk

Install Gentoo Linux on /dev/sda

```bash
printf "g\nn\n1\n\n+256M\nt\nuefi\nn\n2\n\n\nt\n2\nlinux\np\nw\n" | fdisk /dev/sda
sync
```

```bash
mkfs.vfat -F 32 /dev/sda1
mkfs.ext4 -F /dev/sda2
```

## Mount filesystem

```bash
mkdir -p /mnt/gentoo
mount /dev/sda2 /mnt/gentoo/
```

## Download and unpack tarball

```bash
cd /mnt/gentoo
wget https://mirrors.tuna.tsinghua.edu.cn/gentoo/releases/amd64/autobuilds/20220911T170535Z/stage3-amd64-openrc-20220911T170535Z.tar.xz
tar xpvf stage3-*.tar.xz --xattrs-include='*.*' --numeric-owner
```

## Configuring compile options

```bash
sed -i 's/COMMON_FLAGS="-O2 -pipe"/COMMON_FLAGS="-march=native -O2 -pipe"/' /mnt/gentoo/etc/portage/make.conf
echo MAKEOPTS="\"-j$(nproc)\"" >> /mnt/gentoo/etc/portage/make.conf
```

## Set Gentoo mirror

```bash
echo 'GENTOO_MIRRORS="https://mirrors.tuna.tsinghua.edu.cn/gentoo"' >> /mnt/gentoo/etc/portage/make.conf
```

## Configure eBuild repository

```bash
mkdir --parents /mnt/gentoo/etc/portage/repos.conf
cp /mnt/gentoo/usr/share/portage/config/repos.conf /mnt/gentoo/etc/portage/repos.conf/gentoo.conf
sed -i 's/sync-uri = rsync:\/\/rsync.gentoo.org\/gentoo-portage/sync-uri = rsync:\/\/mirrors.tuna.tsinghua.edu.cn\/gentoo-portage/' /mnt/gentoo/etc/portage/repos.conf/gentoo.conf
```

## Copy DNS info

```bash
cp --dereference /etc/resolv.conf /mnt/gentoo/etc/
```

## Mount necessary filesystems

```bash
mount --types proc /proc /mnt/gentoo/proc
mount --rbind /sys /mnt/gentoo/sys
mount --make-rslave /mnt/gentoo/sys
mount --rbind /dev /mnt/gentoo/dev
mount --make-rslave /mnt/gentoo/dev
mount --bind /run /mnt/gentoo/run
mount --make-slave /mnt/gentoo/run
```

## Entering the new environment

```bash
chroot /mnt/gentoo /bin/bash
```

```bash
source /etc/profile
```

## Mounting the boot partition

```bash
mount /dev/sda1 /boot
```

## Installing a Gentoo ebuild repository snapshot from the web

```bash
emerge-webrsync
emerge --sync
```

## Choosing the right profile

```bash
eselect profile list
eselect profile set 1
```

## Updating the @world set

```bash
emerge --verbose --update --deep --newuse @world
```

## Accept all license

```bash
echo "ACCEPT_LICENSE=\"*\"" >> /etc/portage/make.conf
```

## Timezone

```bash
echo "Asia/Shanghai" > /etc/timezone
emerge --config sys-libs/timezone-data
```

## Configure locales

```bash
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
echo "zh_CN.UTF-8 UTF-8" >> /etc/locale.gen
locale-gen
eselect locale list
eselect locale set 4
env-update && source /etc/profile
```

## Installing the sources

```bash
emerge sys-kernel/gentoo-sources
eselect kernel list
eselect kernel set 1
ls -l /usr/src/linux
```

## Kernel configuration and compilation

```bash
emerge sys-kernel/genkernel
echo "/dev/sda1 /boot vfat defaults 0 2" >> /etc/fstab
genkernel all
ls /boot/vmlinu* /boot/initramfs*
echo "/dev/sda2 / ext4 defaults 0 0" >> /etc/fstab
```

## Host and domain information

```bash
echo "hostname=\"hxp-server\"" > /etc/conf.d/hostname
echo "dns_domain_lo=\"homenetwork\"" > /etc/conf.d/net
echo "nis_domain_lo=\"my-nisdomain\"" > /etc/conf.d/net
emerge --noreplace net-misc/netifrc
echo "config_ens192=\"dhcp\"" > /etc/conf.d/net
cd /etc/init.d
ln -s net.lo net.ens192
rc-update add net.ens192 default
```

## Set root password

```bash
passwd root
```

## System logger

```bash
emerge app-admin/sysklogd
rc-update add sysklogd default
```

## Cron daemon

```bash
emerge sys-process/cronie
rc-update add cronie default
```

## File indexing

```bash
emerge sys-apps/mlocate
```

## Remote access

```bash
rc-update add sshd default
```

## Filesystem tools

```bash
emerge sys-fs/e2fsprogs sys-fs/xfsprogs sys-fs/dosfstools
```

## Installing a DHCP client

```bash
emerge net-misc/dhcpcd
```

## Install GRUB2 boot loader

```bash
echo 'GRUB_PLATFORMS="efi-64"' >> /etc/portage/make.conf
emerge sys-boot/grub:2
grub-install --target=x86_64-efi --efi-directory=/boot
grub-mkconfig -o /boot/grub/grub.cfg
mkdir /boot/EFI/boot
cp /boot/EFI/gentoo/grubx64.efi /boot/EFI/boot/bootx64.efi
```

## Adding a user for daily use

```bash
useradd -m -G users,wheel,audio -s /bin/bash hxp
passwd hxp
```

## Rebooting the system

```bash
exit
cd
umount -l /mnt/gentoo/dev{/shm,/pts,}
umount -R /mnt/gentoo
reboot
```
