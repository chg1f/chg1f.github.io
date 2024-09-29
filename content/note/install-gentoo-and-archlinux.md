---
title: "Install Gentoo/Archlinux"
date: 2020-12-06T09:13:12+08:00
tags: ["note", "draft"]
---

# Keyboard
> 之前一直在使用LControl和CapsLock交换的Keyboard Layout，但还没好好清晰地理一遍Linux相关的Keyboaed设置，此文章使用Linux发行版为Kali，以Debian为基础

## 相关包

### `systemd`

在大多数的发行版下，在使用`systemd`作为`init system`，可以使用其中工具`localectl`设置`CLI`及`GUI`的键盘布局

> 在Debian下，安装`console-data`，键盘布包会使用`kmap`为文件后缀，但`localectl`使用`map`为后缀，所以Debian建议使用`dpkg-reconfigure console-data`及`dpkg-reconfigure keyboard-configuration`进行配置，相关ISSUS https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=790955

```bash
$ localectl --help
localectl [OPTIONS...] COMMAND ...

Query or change system locale and keyboard settings.

Commands:
  status                   Show current locale settings
  set-locale LOCALE...     Set system locale
  list-locales             Show known locales
  set-keymap MAP [MAP]     Set console and X11 keyboard mappings
  list-keymaps             Show known virtual console keyboard mappings
  set-x11-keymap LAYOUT [MODEL [VARIANT [OPTIONS]]]
                           Set X11 and console keyboard mappings
  list-x11-keymap-models   Show known X11 keyboard mapping models
  list-x11-keymap-layouts  Show known X11 keyboard mapping layouts
  list-x11-keymap-variants [LAYOUT]
                           Show known X11 keyboard mapping variants
  list-x11-keymap-options  Show known X11 keyboard mapping options

Options:
  -h --help                Show this help
     --version             Show package version
     --no-pager            Do not pipe output into a pager
     --no-ask-password     Do not prompt for password
  -H --host=[USER@]HOST    Operate on remote host
  -M --machine=CONTAINER   Operate on local container
     --no-convert          Don\'t convert keyboard mappings

See the localectl(1) man page for details.
$ strace localectl list-keymaps 2>&1 | grep lstat
lstat("/usr/share/keymaps",...
lstat("/usr/share/kbd/keymaps",...
lstat("/usr/lib/kbd/keymaps",...
lstat("/lib/kbd/keymaps",...
```

### `kbd`

提供在`TTY`/`Console`提供底层键盘工具

```bash
$ apt-file list kbd | grep bin
kbd: /bin/chvt
kbd: /bin/dumpkeys
kbd: /bin/fgconsole
kbd: /bin/kbd_mode
kbd: /bin/loadkeys
kbd: /bin/openvt
kbd: /bin/setfont
kbd: /bin/unicode_start
kbd: /sbin/kbdrate
kbd: /usr/bin/codepage
kbd: /usr/bin/deallocvt
kbd: /usr/bin/getkeycodes
kbd: /usr/bin/kbdinfo
kbd: /usr/bin/loadunimap
kbd: /usr/bin/mapscrn
kbd: /usr/bin/mk_modmap
kbd: /usr/bin/psfaddtable
kbd: /usr/bin/psfgettable
kbd: /usr/bin/psfstriptable
kbd: /usr/bin/psfxtable
kbd: /usr/bin/resizecons
kbd: /usr/bin/screendump
kbd: /usr/bin/setkeycodes
kbd: /usr/bin/setleds
kbd: /usr/bin/setlogcons
kbd: /usr/bin/setmetamode
kbd: /usr/bin/setvtrgb
kbd: /usr/bin/showconsolefont
kbd: /usr/bin/showkey
kbd: /usr/bin/splitfont
kbd: /usr/bin/unicode_stop
kbd: /usr/sbin/setvesablank
kbd: /usr/sbin/vcstime
```

配置文件`/etc/vconsole.conf`


### `x11-xkb-utils`

提供在`X11`下的底层键盘工具（`X11`为Linux主流的`GUI`协议标准）

```bash
$ apt-file list x11-xkb-utils | grep bin
x11-xkb-utils: /usr/bin/setxkbmap
x11-xkb-utils: /usr/bin/xkbbell
x11-xkb-utils: /usr/bin/xkbcomp
x11-xkb-utils: /usr/bin/xkbevd
x11-xkb-utils: /usr/bin/xkbprint
x11-xkb-utils: /usr/bin/xkbvleds
x11-xkb-utils: /usr/bin/xkbwatch
```

配置文件`/etc/X11/xorg.conf.d/*`

## 常用设置

### CapsLock及LControl互换

#### GUI
```bash
$ grep -E "(ctrl|caps):" /usr/share/X11/xkb/rules/base.lst
  ctrl:nocaps          Caps Lock as Ctrl
  ctrl:lctrl_meta      Left Ctrl as Meta
  ctrl:swapcaps        Swap Ctrl and Caps Lock
  ctrl:swapcaps_hyper  Caps Lock as Control, Control as Hyper
  ctrl:ac_ctrl         At left of 'A'
  ctrl:aa_ctrl         At bottom left
  ctrl:rctrl_ralt      Right Ctrl as Right Alt
  ctrl:menu_rctrl      Menu as Right Ctrl
  ctrl:swap_lalt_lctl  Swap Left Alt with Left Ctrl
  ctrl:swap_lwin_lctl  Swap Left Win with Left Ctrl
  ctrl:swap_rwin_rctl  Swap Right Win with Right Ctrl
  ctrl:swap_lalt_lctl_lwin Left Alt as Ctrl, Left Ctrl as Win, Left Win as Left Alt
  caps:internal        Caps Lock uses internal capitalization; Shift "pauses" Caps Lock
  caps:internal_nocancel Caps Lock uses internal capitalization; Shift does not affect Caps Lock
  caps:shift           Caps Lock acts as Shift with locking; Shift "pauses" Caps Lock
  caps:shift_nocancel  Caps Lock acts as Shift with locking; Shift does not affect Caps Lock
  caps:capslock        Caps Lock toggles normal capitalization of alphabetic characters
  caps:shiftlock       Caps Lock toggles ShiftLock (affects all keys)
  caps:swapescape      Swap ESC and Caps Lock
  caps:escape          Make Caps Lock an additional Esc
  caps:escape_shifted_capslock Make unmodified Caps Lock an additional Esc, but Shift + Caps Lock behaves like regular Caps Lock
  caps:backspace       Make Caps Lock an additional Backspace
  caps:super           Make Caps Lock an additional Super
  caps:hyper           Make Caps Lock an additional Hyper
  caps:menu            Make Caps Lock an additional Menu key
  caps:numlock         Make Caps Lock an additional Num Lock
  caps:ctrl_modifier   Caps Lock is also a Ctrl
  caps:none            Caps Lock is disabled
$ setxkbmap -option ctrl:swapcpas  # enbale swap caps and ctrl, multiple option sep with `,`
$ setxkbmap -option  # disable xkb options
```

### CLI

TODO...

```bash
$ cat personal.map
...
keycode 29 = Caps_Lock
keycode 58 = Control
...
$ loadkeys personal.map
```

### 参考资料

- [keyboard configure](https://wiki.archlinux.org/title/Category:Keyboard_configuration)
- [linux console/keyboard](https://wiki.archlinux.org/title/Linux_console/Keyboard_configuration)
- [xorg/keyboard](https://wiki.archlinux.org/title/Xorg/Keyboard_configuration)

## X11
xorg

`Linux`图形化上有一个框架/标准/协议(?)叫`X11`,其中`Xorg`就是他的实现,在各种`DM`,`DE`,`WM`上都使用相应接口进行,也是对多个不同之间框架模块兼容的理由
## DE Desktop Environment
## DM Display Manage
## WM Window Manage

## FlashCD
> 做一个多分区，有加密盘的U盘



```bash
# find mount point
mount -l
# umount
umount <mount-point>
# make partional
sudo fidsk <disk> # input m get help
# format usb disk
sudo mke2fs -j -F -L unencrypted <unencrypted disk>

sudo cryptsetup luksFormat <encrypted disk>
sudo cryptsetup luksOpen <encrypted> encrypted
sudo mke2fs -j -F -L encrypted /dev/mapper/encrypted
sudo cryptsetup luksClose encrypted

## Install
```

>https://linux.cn/article-9640-1.html

> 这理又重装了Gentoo,Archlinux是之前安装的,只在记忆中简单对比一下大概的

> *约定名词*
> 安装环境: 安装Gentoo/Archlinux的操作环境,非安装后的新系统环境,通常为LiveCD的Live环境中
> 目标环境: 安装Gentoo/Archlinux的新系统环境,为安装后结果,此文以`/mnt`为挂载目标(与Archlinux一致,Gentoo官方文档是以`/mnt/gentoo`为例)

## 一点小理解

> https://www.gentoo.org/
> https://www.archlinux.org/

| |Gentoo|Archlinux|
|-|-|-|
|一句话|配置自由度极高(也有人说OverEninerring)|KISS(Keep it Simple, Stupid)|
|硬件支持|反正内核自已编译程序自已编译...|官方支持x86|
|INIT|OpenRC(default)/Systemd|Systemd|
|包管理|portage(源码编译,提供`USE`,`SLOT`等超赞功能)|pacman(二进制分发)|
|安装环境|基本完全不依赖LiveCD,可在任何安装环境下使用stage包开始构建新系统|依赖LiveCD一些工具|


## 玩起来

1. Gentoo
    > https://wiki.gentoo.org/wiki/Installation
    > https://wiki.gentoo.org/wiki/Handbook:Main_Page

2. Archlinux
    > https://wiki.archlinux.org/index.php/installation_guide


### 安装介质

[制作LiveUSB](https://blog.chongiofai.com/note/make-livecd)
> TODO:

1. Gentoo
    > https://www.gentoo.org/downloads/

2. Archlinux
    > https://archlinux.org/download/

### 配置安装环境

1. Network
    ```bash
    $ ip address
    ```

2. Date
    ```bash
    $ date
    $ ntpdate -u cn.pool.ntp.org
    ```

3. Keymap

### 磁盘分区

[Disk Partitional](https://blog.chongiofai.com/note/partition-disk)

1. 准备安装磁盘
    ```bash
    # 查看当前磁盘挂载情况,确定使用哪块磁盘
    $ sudo lsblk
    $ sudo blkid
    # 磁盘分区,以`/dev/sda`为例,简单区分`/`,`/home`,`/boot`,`/boot/efi`,此处使用UEFI方式躯动
    $ gdisk /dev/sda
    ```

1. 磁盘格式化/加密/逻辑分区
    ```bash
    # 此处只作普通分区
    $ mkfs.ext4 -L GENTOO /dev/sda1
    $ mkfs.vfat -L  /dev/sda2
    $ mkswap -L SWAP /dev/sda3
    $ mkfs.ext4 -L GENTOO /dev/sda4
    $ mkfs.ext4 -L GENTOO /dev/sda5
    ```

1. 挂载磁盘
    ```bash
    $ mount /dev/sda4 /
    $ mkdir -p /mnt/boot/efi
    $ mkdir -p /mnt/home
    $ mount /dev/sda1 /mnt/boot
    $ mount /dev/sda2 /mnt/boot/efi
    $ mount /dev/sda5 /mnt/home
    $ swapon /dev/sda3
    # 下方在Archlinux中可以使用LiveCD工具`arch-chroot /mnt`时自动完成,Gentoo需自行手动操作
    $ mount --types proc /proc /mnt/proc
    $ mount --rbind /sys /mnt/sys
    $ mount --make-rslave /mnt/sys  # 使用Systemd时必须
    $ mount --rbind /dev /mnt/dev
    $ mount --make-rslave /mnt/dev  # 使用Systemd时必须
    ```

### 安装工具链
1. Gentoo
    ```bash
    $ tar xvf stage3-amd64-20201230T214503Z.tar.xz -C /mnt
    # 选择校验下载源
    $ mirrorselect -i -o >> /mnt/etc/portage/make.conf
    # 复制
    ```
    > 进入环境后
    ``` bash
    # 配置下载源
    $ mkdir -p /etc/portage/repors.conf
    $ cp /usr/share/portage/config/repos.conf /etc/portage/repors.conf/gentoo.conf
    $ cat /etc/portage/repors.conf/gentoo.conf
    # 时间同步,Gentoo安装拉取会使用到`rsync`等依赖系统时间
    $ ntpdate -u cn.pool.ntp.org
    # 下载文件校验码
    $ emerge-webrsync
    # 下载文件目录
    $ emerge --sync
    # 确定portage配置
    $ cat /etc/portage/make.conf
    # 选择配置文件
    $ eselect profile list
    $ eselect profile set 1
    $ eselect profile list
    ```


2. Archlinux
    ```bash
    $ pacstrap /mnt base
    ```

### 安装内核

1. Gentoo

    > https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Kernel

     ```bash
     $ emerge --ask --verbose sys-apps/pciutils app-admin/mcelog
     $ emerge --ask --verbose sys-kernel/gentoo-sources
     $ cd /usr/src/linux
     $ make menuconfig
     $ cat /usr/src/linux/.config
     $ make
     $ make modules_install
     $ make install
     $ ls /boot/vmlinuz-*-gentoo-*  /boot/config-*-gentoo-* /boot/System.map-*-gentoo-*
     $ genkenerl
     $ ls /boot/initramfs-*-gentoo-*
     ```

2. Archlinux

    ```bash
    $ pacstrap /mnt linux
    ```
### 安装驱动
1. Gentoo

    ```bash
    $ emerge --ask --verbose sys-kernel/linux-firmware
    ```

2. Archlinux

    ```bash
    $ pacstrap /mnt linux-firmware
    ```


### 安装引导

[Boot Device](https://blog.chongiofai.com/note/boot-device)

1. Gentoo

    ```bash
    $ emerge --ask --verbose sys-boot/grub:2
    ```

2. Archlinux


### 个人化
1. locale,keymap
2. hostname, domain
3. hosts
4. passwd,useradd

### 其他

#### 图形化
[Linux Graphics](https://blog.chongiofai.com/note/linux-graphics)



1. Gentoo
2. Archlinux




1. 安装发行版工具链,如init(Archlinux的`Systemd`和Gentoo可选的`Systemd`及`OpenRC`)、包管理工具(Archlinux的`pacman`和Gentoo的`portage`)等等

   - Archlinux

     ```bash
     pacstrap /mnt base
     ```

   - Gentoo

     > https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Stage

     ```bash
     tar xf stage3-amd64-20201202T214503Z.tar.xz  # 这里使用OpenRC版本init
     ```

2. 工具链配置

2. 安装Linux的内核


3. 安装基本驱动集合

   - Archlinux

     ```bash
     pacstrap /mnt linux-firmware
     ```

   - Gentoo

     ```bash
     emerge --ask sys-kernel/linux-firmware
     ```
4. 启动
   `grub-install --efi`....
   `update-grub`
4. 本地化
5. 网络配置
5. 图形化(Optional)

> TODO:


## 参考资料

https://wiki.gentoo.org/

http://wiki.archlinux.org/

https://www.thegeekstuff.com/2011/02/linux-boot-process/


## 后续

# GUI

# LiveCD


## Install LiveCD

```bash
dd status=progress if=livecd.iso of=/dev/sdaX bs=512
fdisk ... # TODO: fix disk size
cryptsetup luksFormt /dev/sdaX
cryptsetup open persistence /dev/sdaX
mkfs.ext4 -L persistence /dev/mapper/persistence
mount /dev/mapper/persistence /mnt/livecd
echo "/ union" > /mnt/livecd/persistence.conf
umount /dev/mapper/persistence
cryptsetup close persistence
```

##

1. backup dick images
```bash
$ fdisk -l
$ blkid
$ dd status=progress bs=512 if=/dev/sda1 | gzip -nc -6 > backup.img.gz
# status
# bs
# if
```




## 参考资料

https://www.kali.org/downloads/

https://www.kali.org/docs/usb/kali-linux-live-usb-install-linux/

https://www.kali.org/docs/usb/kali-linux-live-usb-persistence/



## 后续

TODO: 先把记得的先写下,用了一下Kali好像用了个一次性的内核,然后每次rsync到持久化分区保存文件,有空也可以再探究一下

# OS

# Processing

# Diff


