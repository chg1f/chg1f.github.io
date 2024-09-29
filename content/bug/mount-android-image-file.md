---
title: "Bug Mount Android Image File"
date: 2020-12-09T13:12:12+08:00
categories:
- bug
tags:
- android
- mount
---

## Question

```bash
$ file system.img
system.img: Linux rev 1.0 ext2 filesystem data, UUID=4a50462e-52cf-5fab-87a1-49824cdf6b4f (extents) (large files) (huge files)
$ sudo mount -t auto system.img system
mount: system: failed to setup loop device for system.img.
$ sudo mount -t ext2 system.img system
mount: system: wrong fs type, bad option, bad superblock on /dev/loop0, missing codepage or helper program, or other error.

```

## Tried

1. mount with loop,offset
```bash
$ file system.img
system.img: Linux rev 1.0 ext2 filesystem data, UUID=4a50462e-52cf-5fab-87a1-49824cdf6b4f (extents) (large files) (huge files)
$ parted system.img print
WARNING: You are not superuser.  Watch out for permissions.
Model:  (file)
Disk system.img: 1840MB
Sector size (logical/physical): 512B/512B
Partition Table: loop
Disk Flags:

Number  Start  End     Size    File system  Flags
 1      0.00B  1840MB  1840MB  ext2
$ sudo mount -o loop,offset=0 system.img system
mount: system: wrong fs type, bad option, bad superblock on /dev/loop0, missing codepage or helper program, or other error.
$ sudo mount -t ext2 -o loop,offset=0 system.img system
mount: system: wrong fs type, bad option, bad superblock on /dev/loop0, missing codepage or helper program, or other error.
```

2. mount with losetup
```bash
$ ls /dev/loop0
/dev/loop0
$ sudo losetup -f
/dev/loop0
$ sudo losetup /dev/loop0 system.img
$ sudo mount /dev/loop0 system
mount: system: wrong fs type, bad option, bad superblock on /dev/loop0, missing codepage or helper program, or other error.
$ sudo losetup  -d /dev/loop0
``
> ```bash
> [10021.208192] EXT4-fs (loop0): couldn't mount RDWR because of unsupported optional features (4000)
> [10247.432930] EXT4-fs (loop0): couldn't mount RDWR because of unsupported optional features (4000)
> [10319.835415] EXT4-fs (loop0): couldn't mount as ext2 due to feature incompatibilities
> [10390.662357] EXT4-fs (loop0): couldn't mount RDWR because of unsupported optional features (4000)
> [10757.923783] EXT4-fs (loop0): couldn't mount RDWR because of unsupported optional features (4000)
> ```

