---
layout: post
title: "Some notes on BlackArch Linux upgrading"
subtitle: 'Upgrade Arch Linux to BlackArch Linux for penetrate testing'
author: "hxp"
header-style: text
tags:
- Linux 
---

[BlackArch Linux](https://blackarch.org) is a Linux distribution based on Arch Linux, aiming at penetrate testing. It is somewhat an alternative to Kali Linux. Most importantly, Arch Linux can be upgraded to BlackArch directly. So if you are using Arch Linux currently as I do, and if you need Kali Linux, it is a good choice to upgrade your Arch Linux to BlackArch instead of repartitioning your disk and install Kali Linux.

To start the upgrading process, follow this [installation guide](https://blackarch.org/downloads.html). But there is something to notice.

run

``` shell
curl -O https://blackarch.org/strap.sh
chmod +x strap.sh
sudo ./strap.sh
sudo pacman -S blackarch
```

To install as the official guide told us.

But, the installation was not so successfully for me. At first, it said

``` shell
error: failed to prepare transaction (could not satisfy dependencies)
:: removing deepin-udis86 breaks dependency 'deepin-udis86' required by deepin-wine
```

The solution was to remove `deepin-udis86' and all the dependencies manually. And then install again.

``` shell
sudo pacman -Rcns deepin-udis86
sudo pacman -S blackarch
```

And another error occurred.

``` shell
error: failed retrieving file 'aesshell-0.7-4-any.pkg.tar.xz' from ftp.halifax.rwth-aachen.de : Operation too slow. Less than 1 bytes/sec transferred the last 10 seconds---------------]  38%
warning: failed to retrieve some files
```

That is because I use [TUNA mirror](https://mirrors.tuna.tsinghua.edu.cn/) to download packages quicker in China. Some packages are included in the mirror, while others are not. And the connection to foreign servers are not stable.

I have a V2Ray listening on localhost. Use the proxy to optimize the connection works.

Edit the environment variable to use proxy for pacman

``` shell
export https_proxy="http://localhost:1080"
export http_proxy="http://localhost:1080" 
```

And install BlackArch again

``` shell
sudo pacman -S blackarch
```
