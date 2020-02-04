# Way to delete old kernel and release disk space in /boot #

## Case: /boot partition is 100% used and apt malfunctions ##

When I tired to install nginx in Ubuntu 14.04 server, I noticed that whatever package I install by using `apt install`, error massage like this emerges.

``` shell
The following packages have unmet dependencies: 
	linux-image-generic : 
		Depends: linux-image-4.4.0-150-generic but it is not installed or
		         linux-image-unsigned-4.4.0-150-generic but it is not installed             
		Recommends: thermald but it is not installed
	linux-modules-extra-4.4.0-150-generic : 
		Depends: linux-image-4.4.0-150-generic but it is not installed or
			 linux-image-unsigned-4.4.0-150-generic but it is not installed
```

Also, I've attempted to update the Linux kernel by using the script provided by [teddysun](https://teddysun.com/489.html), but failed.

I also tried installing nginx by using the command `apt -f install nginx`, it turned out to be the same output as above. And `apt autoremove` does not take effect.

## Solution to this problem ##

### Detect if /boot partition is fully occupied ###

Run `df -lh`

``` shell
root@localhost:~# df -lh
Filesystem      Size  Used Avail Use% Mounted on
udev            217M     0  217M   0% /dev
tmpfs            50M  9.7M   40M  20% /run
/dev/sda2       9.5G  3.3G  5.7G  37% /
tmpfs           247M     0  247M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           247M     0  247M   0% /sys/fs/cgroup
/dev/sda1       361M  361M    0M 100% /boot
tmpfs            50M     0   50M   0% /run/user/0
```

It seems that the /boot partition is full. So that new kernel cannot be installed.

### List all the kernels ###

``` shell
sudo dpkg --list 'linux-image*'|awk '{ if ($1=="ii") print $2}'|grep -v `uname -r`
```

The output may be something like below

``` shell
linux-image-3.19.0-25-generic
linux-image-3.19.0-56-generic
linux-image-3.19.0-58-generic
linux-image-3.19.0-59-generic
linux-image-3.19.0-61-generic
linux-image-3.19.0-65-generic
linux-image-extra-3.19.0-25-generic
linux-image-extra-3.19.0-56-generic
linux-image-extra-3.19.0-58-generic
linux-image-extra-3.19.0-59-generic
linux-image-extra-3.19.0-61-generic
```

### Force remove old kernels ###

Use `dpkg -P --force-depends` to force remove the kernel file. `apt purge` did not work for me.

For example:

``` shell
sudo  dpkg -P --force-depends linux-image-4.4.0-134-generic
```



