---
title: "Grub"
date: 2020-01-26
tags: ["grub"]
---

> 之前grub搞了个在U盘上引就安装系统，不过引导不了windows的安装装，和和和之前又洗了，之后再玩玩，也想想要怎么玩，因为也想搞个linux在U盘上
> 不过今天看到双系统的引导,一来想把倒数关了,免自己老要开机时守那几秒,和上想是找方法想弄成,自动启动上次开启的系统,和和也再好好看看grub

## 目录
> 自已之前看过一次文档(都忘了),现在再撸一下目撸猜一下,纪录一下

1. /boot/grub/*
    引导盘引导程序核心位置，所有引导需要的材料都放在这里了
  1. /boot/grub/grub.cfg

    引导时的配置都看他了，不过好像是从grub2开始这个是自动生成的，改这如果重刷了还是会改回去，不优雅

2. /etc/grub.d/*
    好像从grub2版本可以用命令按这个路径下的配置把/boot/grub/grub.cfg渲染出来， 看下README

```
All executable files in this directory are processed in shell expansion order.

  00_*: Reserved for 00_header.
  10_*: Native boot entries.
  20_*: Third party apps (e.g. memtest86+).

  The number namespace in-between is configurable by system installer and/or
   administrator.  For example, you can add an entry to boot another OS as
   01_otheros, 11_otheros, etc, depending on the position you want it to occupy in
   the menu; and then adjust the default setting via /etc/default/grub.
```
  大概定义了一下各文件的渲染上的任务
```
  .
├── 00_header
├── 05_debian_theme
├── 10_linux
├── 20_linux_xen
├── 20_memtest86+
├── 30_os-prober
├── 30_uefi-firmware
├── 40_custom
#!/bin/sh
exec tail -n +3 $0
# This file provides an easy way to add custom menu entries.  Simply type the
# menu entries you want to add after this comment.  Be careful not to change
# the 'exec tail' line above.
├── 41_custom
#!/bin/sh
if [ -f  \${config_directory}/custom.cfg ]; then
  source \${config_directory}/custom.cfg
elif [ -z "\${config_directory}" -a -f  \$prefix/custom.cfg ]; then
  source \$prefix/custom.cfg;
fi
└── README
```
  就如上。。基本就这样，按他格化式加下配置就好，优雅点

3. /etc/default/grub
    这就是渲染grub.cfg时的环境变量，原版（我好像没改过）就酱，改个timeout为0，先把那个倒数启动关了
```
  # If you change this file, run 'update-grub' afterwards to update
  # /boot/grub/grub.cfg.
  # For full documentation of the options in this file, see:
  #   info -f grub -n 'Simple configuration'

  GRUB_DEFAULT=0
  GRUB_TIMEOUT_STYLE=hidden
  GRUB_TIMEOUT=10
  GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
  GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
  GRUB_CMDLINE_LINUX=""

  # Uncomment to enable BadRAM filtering, modify to suit your needs
  # This works with Linux (no patch required) and with any kernel that obtains
  # the memory map information from GRUB (GNU Mach, kernel of FreeBSD ...)
  #GRUB_BADRAM="0x01234567,0xfefefefe,0x89abcdef,0xefefefef"

  # Uncomment to disable graphical terminal (grub-pc only)
  #GRUB_TERMINAL=console

  # The resolution used on graphical terminal
  # note that you can use only modes which your graphic card supports via VBE
  # you can see them in real GRUB with the command `vbeinfo'
  #GRUB_GFXMODE=640x480

  # Uncomment if you don't want GRUB to pass "root=UUID=xxx" parameter to Linux
  #GRUB_DISABLE_LINUX_UUID=true

  # Uncomment to disable generation of recovery mode menu entries
  #GRUB_DISABLE_RECOVERY="true"

  # Uncomment to get a beep at grub start
  #GRUB_INIT_TUNE="480 440 1"
```

## 命令
1. update-grub
    重新渲染/boot/grub/grub.cfg
