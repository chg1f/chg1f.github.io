---
title: "Kali localectl list-keymaps get no such file or directory Error"
description: ""
date: 2021-05-03T22:14:49+08:00
lastmod: 2021-05-03T22:14:49+08:00
tags:
- keyboard
- linux
- debian
---

## Environment

```bash
$ cat /etc/*release
PRETTY_NAME="Kali GNU/Linux Rolling"
NAME="Kali GNU/Linux"
ID=kali
VERSION="2021.1"
VERSION_ID="2021.1"
VERSION_CODENAME="kali-rolling"
ID_LIKE=debian
ANSI_COLOR="1;31"
HOME_URL="https://www.kali.org/"
SUPPORT_URL="https://forums.kali.org/"
BUG_REPORT_URL="https://bugs.kali.org/"
```

## Question

```bash
$ localectl list-keymaps
Failed to read list of keymaps: No such file or directory
```

## Debugging

> https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=790955

```bash
$ ls /usr/share/keymaps/
ls: cannot access '/usr/share/keymaps/': No such file or directory
$ ls /usr/share/kbd/keymaps
ls: cannot access '/usr/share/kbd/keymaps': No such file or directory
$ strace localectl list-keymaps 2>&1 | less
...
lstat("/usr/share/kbd/keymaps", 0x7ffe375fa420) = -1 ENOENT (No such file or directory)
lstat("/usr/lib/kbd/keymaps", 0x7ffe375fa420) = -1 ENOENT (No such file or directory)
lstat("/lib/kbd/keymaps", 0x7ffe375fa420) = -1 ENOENT (No such file or directory)
...
$ sudo mv /usr/share/keymaps/i386/qwerty/us.kmap.gz /usr/share/keymaps/i386/qwerty
/us.map.gz
$ localectl list-keymaps
us

```

## Fixed

1. `/usr/share/keymaps`等没有`keymap`文件是因为没安装`console-data`
```bash
$ sudo apt install console-data
```
2. 根据上面的引用`issus`好像是`debian`一些兼容性的不同,官方推荐使用别的方式去设置键盘
> https://benohead.com/blog/2012/04/27/debian-change-the-keyboard-layout-from-the-console/
>
> https://wiki.debian.org/Keyboard
```bash
$ dpkg-reconfigure console-data
$ dpkg-reconfigure keyboard-configuration
```
