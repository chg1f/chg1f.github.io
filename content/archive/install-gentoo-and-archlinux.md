---
title: "Install Gentoo/Archlinux"
date: 2020-12-06T09:13:12+08:00
toc: true
categories:
- note
tags:
- linux
- gentoo
- archlinux
- install
---

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
