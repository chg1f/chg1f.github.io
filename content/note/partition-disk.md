---
title: "Partition Disk" # Title of the blog post.
date: 2021-01-02
updated: 2021-01-02
toc: true
categories:
  - note
tags:
  - partition
  - disk
  - hardware
---

## Partition Tool
> `fdisk`和`parted`都是分区实用程序。 `fdisk`是众所周知的，稳定的，并且推荐用于MBR分区布局，而`parted`是支持GPT分区的第一个Linux块设备管理实用程序之一。 喜欢`fdisk`的用户界面的用户可以使用 `gdisk` （GPT fdisk）作为`parted`的替代。
### parted
### fdisk
### gdisk
## Partition Table
### MBR
主引导记录MBR (Master Boot Record)对分区的起始扇区和长度使用32比特的标识符，它支持三种分区类型：主分区、扩展分区和逻辑分区。主分区把它们的信息存储在主引导记录里一个非常小的空间里，通常是512字节，因为这个空间太小了，所以系统只支持四个主分区。（例如从 /dev/sda1 到/dev/sda4）
### GPT
GPT(GUID Partition Table)为分区使用64比特标识符，它用来存储分区信息的空间也远比MBR的512字节要大，GPT磁盘它也不对分区的数量作限制。分区的大小限制可以达到8ZiB。 译者注：
```
1ZiB = 1,024 EiB
1EiB = 1024 PiB
1PiB = 1024 TiB
1TiB = 1024 GiB
1GiB = 1024 MiB
1MiB = 1024 KiB
1KiB = 1024 B
```
当操作系统和系统固件之间的软件接口是UEFI (相对于BIOS)时，GPT几乎是必选的，因为这里MBR会引起很多兼容性问题。

GPT还利用校验和和冗余。 它携带CRC32校验和以检测报头和分区表中的错误，并在磁盘的末尾有一个备份GPT。 此备份表可用于恢复磁盘开头附近主GPT的损坏。
## Partition Foramt
### btrfs
是下一代文件系统，提供了许多高级功能，如快照，通过校验和自我修复、 透明压缩、 子卷和集成 RAID。几个发行版已经开始将它作为一个默认的选项,但它还未为生产工作做好准备。文件系统报告崩溃是常见的。其开发人员敦促人们运行最新的内核版本来解决安全问题,以及老的问题。 这种情况已经很多年了,现在使用它还为时过早。如果出现变更，以及发生了变化，解决错误问题，都很少往旧内核注入补丁。请谨慎使用这个文件系统!
### ext2
是经考验证明可靠的Linux文件系统，但是没有元数据日志，这意味这在启动系统时的ext2文件系统的日常检查相当耗时。现在相当一部分的新一代的日志文件系统都可以非常迅速检查一致性，因此比那些非日志文件系统更受欢迎。当你启动系统碰巧遇到文件系统状态不一致时，日志文件系统不会在那里耽搁很长时间。
### ext3
是ext2文件系统的带日志版本，提供了元数据日志模式以快速恢复数据。此外还提供了其他增强的日志模式，如完整数据日志模式和有序数据日志模式。它使用了HTree索引，在几乎所有的情况下都能保持高性能。简而言之，ext3是非常好及可靠的文件系统。
### ext4
最初创建为ext3的一个分支，EXT4带来了新的功能，性能改进和去除中度更改磁盘格式大小限制。它可以跨越体积高达1的EB并用16 TB最大文件大小。取而代之的是经典的ext2/3位块分配的ext4的使用范围，这对提高大文件的性能，并减少碎片。的Ext4还提供了更为复杂的块分配算法（延迟分配和多嵌段分配）给文件系统驱动更多的方式来优化数据的布局在磁盘上。 EXT4是推荐的通用所有平台的文件系统。
### f2fs
这个文件系统最初由三星创建用于NAND闪存，是一种闪存文件系统 从直到2016年第二季度起，这个文件系统仍然被认为不成熟。把Gentoo安装到microSD卡，USB驱动器或其他基于闪存的存储设备时使用它是一个不错的选择。
### JFS
是IBM的高性能日志文件系统。JFS是一个轻量级的、快速的和稳定的基于B+树的文件系统，在很多情况下都有很好的表现。
### ReiserFS
是基于B+树的文件系统，它有着非常全面的性能，特别时在处理很多小文件的时候，虽然会占用多一点CPU。ReiserFS相比其他文件系统显得受维护的不够。
### XFS
是一种带元数据日志的文件系统，它有一个健壮的特性集，并且对可伸缩性进行了优化。XFS似乎对各种各样的硬件问题显得不够宽容。
### vfat
也称为FAT32，被支持Linux，但不支持任何权限设置。它主要用于互操作性与其他操作系统（主要是微软的Windows），但也是很有必要的一些系统固件（如UEFI）的支持。
### NTFS
这个“新技术”的文件系统是Microsoft Windows的旗舰文件系统。 与上面的vfat类似，它不存储BSD或Linux正常工作所需的权限设置或扩展属性，因此它不能用作根文件系统。 它应该'只'用于与Microsoft Windows系统的互操作性（注意只强调）。
## Other
### LVM
> https://wiki.archlinux.org/index.php/LVM_
> </br>
> https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)
#### PV
1. `pvs`
2. `pvdisplay`
#### VG
1. `vgs`
2. `vgdisplay`
#### LV
1. `lvs`
2. `lvdisplay`
3. `lvextend`
### LUKS
## Mount
### Fstab
> /etc/fstab
> </br>
> https://wiki.gentoo.org/wiki/Fstab
> </br>
> https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/System#About_fstab
> ```
> <filesystem> <mountpoint> <type> <options> <dump> <pass>
> ```
#### filesystem
> 第一个字段显示要挂载的特殊 block 设备或远程文件系统。 有几种设备标识符可用于特殊块设备节点，包括设备文件路径，文件系统标签，UUID，分区标签以及UUID。
1. UUID
2. LABEL
#### mountpoint
> 第二个字段是分区挂载点，也就是分区应该挂载到的地方
#### type
> 第三个字段给出分区所用的文件系统
> </br>
> [Partition Format](http://blog.chongiofai.com/note/partition-disk/#partition-foramt)
#### options
> 第四个字段给出的是挂载分区时mount命令所用的挂载选项。由于每个文件系统都有自己的挂载选项，我们建议你阅读mount手册（man mount）以获得所有挂载选项的列表。多个挂载选项之间是用逗号分隔的。
> </br>
> https://wiki.gentoo.org/wiki/Mount
> </br>
> http://www.skrenta.com/rt/man/mount.8.html
#### dump
> 第五个字段是给dump使用的，用以决定这个分区是否需要dump。一般情况下，你可以把该字段设为0（零）。
#### pass
> 第六个字段是给fsck使用的，用以决定系统非正常关机之后文件系统的检查顺序。根文件系统应该为1，而其它的应该为2（如果不需要文件系统自检的话可以设为0）。

## References
- https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Disks/zh-cn#%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F
