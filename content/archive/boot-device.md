---
title: "Boot Device" # Title of the blog post.
date: 2020-12-23T10:57:10+08:00 # Date of post creation.
# description: "Article description." # Description used for search engine.
# featured: true # Sets if post is a featured post, making appear on the home page side bar.
# menu: main
# featureImage: "/images/path/file.jpg" # Sets featured image on blog post.
# thumbnail: "/images/path/thumbnail.png" # Sets thumbnail image appearing inside card on homepage.
# shareImage: "/images/path/share.png" # Designate a separate image for social media sharing.
# codeMaxLines: 10 # Override global value for how many lines within a code block before auto-collapsing.
# codeLineNumbers: false # Override global value for showing of line numbers within code block.
# figurePositionShow: true # Override global value for showing the figure label.
# comment: false # Disable comment if false.
toc: true # Controls if a table of contents should be generated for first-level links automatically.
categories:
  - note
tags:
---

UEFI/BIOS

**Insert Lead paragraph here.**
1. Linux After Press Power Button

>  https://www.thegeekstuff.com/2011/02/linux-boot-process/
>
>  6 Stages of Linux Boot Process
>
>  BIOS - Basic Input/Output system executes MBR
>
>  MBR - Master Boot Record executes GRUB
>
>  GRUB - Grand Unified Bootloader executes Kernel
>
>  Kernel - Kernel executes /sbin/init
>
>  Init - Init executes runlevel programs
>
>  Runlevel - Runlevel programs are executes from /etc/rc.d/rc*.d/



1. OpenRC VS Systemd

> https://wiki.archlinux.org/index.php/Systemd
>
> https://wiki.gentoo.org/wiki/OpenRC

对于Gentoo默认是使用`OpenRC`,现在大量的Linux发行版都使用`Systemd`(Archlinux也是使用`Systemd`),在看了一些资料后了解,这两个都是作为`PID1`的`init`(其中其实还有一个`SysVinit`,也是大量发行版以前的`init`),且`gnome`也是绑定了`Systemd`,对于`Systemd`可以理解为有高度集成的一个`init`,其中包括`timedatectl`,`systemctl`,`journalctl`,`hostnamectl`等等,还有对很多的操作,有很多对于`Systemd`的负面评价,在看完后装Gentoo时决定,再学习一次试一下`OpenRC`看看还需要什么服务的...

