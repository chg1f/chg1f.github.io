---
title: "Iwlwifi Intel AX210"
description: "Intel AX210网卡兼容问题"
tags:
- linux
- kernel
- iwlwifi
---

## Environment

- Intel® Wi-Fi 6 AX210 160MHz
```bash
$ lspci
...
09:00.0 Network controller [0280]: Intel Corporation Device [8086:2725] (rev 1a)
...
```
- Kali

## Question

```bash
$ sudo dmesg
iwlwifi .. Timeout waiting for PNVM load!
iwlwifi .. Failed to start RT ucode: -110
iwlwifi .. iwl_trans_send_cmd bad state = 0
iwlwifi .. Failed to run INIT ucode: -110
$ ip a
...
(no exists wlan)
...
```

## Debugging

> https://wireless.wiki.kernel.org/en/users/drivers/iwlwifi
>
> https://www.intel.com/content/www/us/en/support/articles/000005511/wireless.html

```bash
$ ls /lib/firmware/iwlwifi-*.ucode
...
iwlwifi-ty-59.601f3a66.0.tgz
...
```

> https://community.intel.com/t5/Wireless/Linux-driver-Wifi-6-AX210-NGW/td-p/1249283
>
> https://www.reddit.com/r/pop_os/comments/khhufu/is_there_a_way_to_make_intel_ax210_wifi_6e/
>
> https://www.kernel.org/
>
> https://www.kali.org/docs/development/recompiling-the-kali-linux-kernel/

```bash
$ ls /usr/src/linux-5.12.3
$ cp /boot/config-...kali...-amd64 .config
$ make menuconfig
# $ make
$ make deb-pkg LOCALVERSION=-custom KDEB_PKGVERSION=$(make kernelversion)-1
$ sudo dpkg -i ../linux-image-5.12.3-custom_5.12.3-1_amd64.deb
$ reboot
```

## Fixed

一开始以为是`linux-firmware`/`iwlwifi`没有最新版本的躯动，检查后发现有
然后是在看发现相关躯动要在`5.11+`和`5.12`在支持
```bash
$ apt search linux-source                        c1f@CIFT14
Sorting... Done
Full Text Search... Done
linux-source/kali-rolling 5.10.28-1kali1 all
  Linux kernel source (meta-package)

linux-source-4.19/oldstable 4.19.181-1~deb9u1 all
  Linux kernel source for version 4.19 with Debian patches

linux-source-4.9/oldstable 4.9.258-1 all
  Linux kernel source for version 4.9 with Debian patches

linux-source-5.10/kali-rolling 5.10.28-1kali1 all
  Linux kernel source for version 5.10 with Debian patches
```
Kali官方上没有支持，自已编译一个，解决


