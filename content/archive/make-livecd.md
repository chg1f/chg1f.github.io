---
title: "LiveCD"
date: 2020-12-06T09:13:12+08:00
categories:
- note
tags:
- livecd
- kali
- linux
---
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
