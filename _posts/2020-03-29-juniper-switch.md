---
layout: post
title: "Installing newer versions of OS on Juniper SRX Series"
subtitle: 'My "hack" Upgrade on Juniper SRX240H switch'
author: "XiaoHe321"
header-style: text
tags:
- Switch
---


# 1.Background

As I first got a Juniper SRX240H switch, someone told me that it can be "hacked" to upgrade to some newer OSes that weren't officially supported. The SRX240H and many other types were limited to use the OS versioned Junos 12.1X47. If you try to force install an OS newer than 12.1X47, installation will be aborted with a message telling you that you can't upgrade to the newer OS. Then "someone" told me you can upgrade by replacing the existing 1G DDR2 memory to the 2G DDR2 memory. But after I replaced it, it didn't work. The message emerged again and hindered me to continue. After nearly a day's googling, finally I got the key.

This passage is based on my experience on SRX240H, but it should work on any other types.

# 2.Summative Steps

If you don't want to see many many explanations, the only chapter that is essential to view is this chapter.

1. Firstly, a 2G DDR2 memory (2Rx8) is needed, a 667MHz one is better. (In my case, my memory's frequency is 667MHz, I'm not sure whether 800MHz works. If you have more money, buy it and test yourself :) .)

2. Download the OS you need from Juniper Website, which should be named like `junos-xxxxx.tgz`.

3. Open it with `7z` on Windows or command-line tools on UNIX-Like systems (I haven't tested on UNIX-Like systems yet, you may try).

4. Open the `+INSTALL` file using 7z's modify function and commented all "Error "Unsupported platform $product_model for 12.1X47 and higher"" by "#".

   Before commenting:

   ```
   Error "Unsupported platform $product_model for 12.1X47 and higher" 
   ```

   After commenting:

   ```
   #Error "Unsupported platform $product_model for 12.1X47 and higher" 
   ```

   There are many lines like that in this file, I suggest using editors to search and replace.

5. Do the same with the `+REQUIRE` file.

6. Repack the tgz file with original orders (Someone said if you just repack but didn't pack in order, the system cannot find the kernel, I tested by replacing files in 7z, it sucks. I don't know how to repack them in order. Although someone said you can use 'tar -tvvf', also, I don't know whether it will work. Test yourself. After install, if the system tells you that it cannot find the kernel, that's probably because you repacked the package incorrectly.)

7. That's all, at this time, your switch may still be using the 1G DDR2 memory. But if you replace the memory prematurely, you won't boot into the system (The old system didn't have the 2G memory profile, you will suck into the db debug mode.)

8. Put the modified OS pack into your device, whether by sftp or a USB pen drive.

9. Login the system, use cli mode, type `request system software add no-copy /path/to/your/modified/package.tgz`.

10. You may see warnings on the screen, ignore them as usual since it won't affect the result.

11. After finishing, shutdown the system instead of rebooting. If you just reboot with the 1G RAM installed, it will work "fine", but after a while, you will find it stuck into a boot loop. Only if you replace the memory with the 2G one, it will finally boot.

12. Replace the 1G memory to 2G memory.

13. Power on, and wait patiently. It will reboot a few times, and if you finally see the login stage, you'd better shutdown and cold boot again, otherwise you will see some action behaves slow and even timeout.

14. After a few reboots, you may try to login. If no error occurs (If any, reboot again), congratulations! The newer version of OS on SRX series is installed successfully!

15. Your conf files should exist, and you can now use the J-Web to manage the switch (The old version of OS's J-Web had bugs that prevents you from signing in)! (Notice: use other browsers rather than Firefox. My Firefox encountered errors, I'm not sure if it was caused by extension(s)' problem)

## 3.Principle

As you can see, `official new OS package` don't allow you to install them on old devices. BUT these limits are only wrote in the pre-install script files. If you modify the script files, these limits are no longer be limits.

Also, why I emphasize the order to replace the memory and installation of OS, that is the old OSes doesn't support 2G RAM, but the new OSes "support" 1G RAM(At least you **CAN** boot up). If you install memory first, you **EVEN CANNOT BOOT INTO THE OS**. Although you can install OS via bootloader, but the bootloader load another pre-install binary file to run the pre-install actions. Notwithstanding you can modify this binary file by some hex editors, but I tried, and failed to boot the installation process (It said something wrong with the hardware watchdog, but I can't search the cause of this on google. If you are interested, you can try it yourself and tell me how)

That's simple enough, isn't it?
