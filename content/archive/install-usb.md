---
title: "U盘制作"
date: 2020-10-12T10:53:57+08:00
---



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
```

>https://linux.cn/article-9640-1.html