---
title: "How to boot device"
date: 2020-12-23T10:57:10+08:00
tags: ["notes"]
---

## Processing of boot device

### Linux
>  https://www.thegeekstuff.com/2011/02/linux-boot-process/
1. Pass Startup
2. BIOS executes MBR/GPT
3. MBR/GPT executes GRUB
4. GRUB executes Kernel
5. Kernel executes /sbin/init
6. Init executes runlevel programs
7. Runlevel programs are executes from /etc/rc.d/rc*.d/

## Init System

> Gentoo default is openrc, and Arch default is systemd, sysvinit is also a init system in early linux. gnome is binded with systemd. systemd is a init system with high integration, including timedatectl, systemctl, journalctl, hostnamectl, etc.

### OpenRC
> https://wiki.gentoo.org/wiki/OpenRC

### Systemd
> https://wiki.archlinux.org/index.php/Systemd

