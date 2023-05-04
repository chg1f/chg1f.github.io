---
title: "Android Flashing Device"
date: 2020-11-23T19:18:05+08:00
toc: true
categories:
- note
tags:
- android
- adb
- fastboot
- root
- oneplus
---

## Android刷机

### 环境

1. OnePlus8Pro

2. Kali Rolling

3. adb

   > Android Debug Bridge version 1.0.39
   > Version 1:8.1.0+r23-8
   > Installed as /usr/lib/android-sdk/platform-tools/adb
   >
   > `sudo apt install android-tools-adb`

4. fastboot

   > fastboot version 1:8.1.0+r23-8
   > Installed as /usr/lib/android-sdk/platform-tools/fastboot
   >
   > `sudo apt install android-tools-fastboot`



### 基础

1. 解锁OEM

   > https://source.android.com/setup/build/running
2. 进入FastbootMode

3. 进入RecoveryMode

4. 安装Magisk/MagiskManager

   > 官方网站：　https://magiskmanager.com/


### 玩起来

1. 官方OTA全量包中解压，此处使用`OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e.zip`

   > https://www.oneplus.com/cn/support/softwareupgrade
   >
   > https://www.oneplus.com/support/softwareupgrade

```
$ unzip OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e.zip -d OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e
$ tree OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e
OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e
├── META-INF
│   └── com
├── payload.bin
└── payload_properties.txt
```
2. 其中`payload.bin`是使用`protobuf`压缩的二进制`img`文件，使用`payload_dumper`解压出来`boot.img`、`recovery.img`及其他`img`文件

   > https://github.com/vm03/payload_dumper

   ```
   $ git clone https://github.com/vm03/payload_dumper.git payload_dumper
   $ pip install -r requirements.txt
   $ python payload_dumper.py payload.bin
   $ tree payload_dumper
   payload_dumper
   ├── old
   ├── output
   │   ├── abl.img
   │   ├── aop.img
   │   ├── bluetooth.img
   │   ├── boot.img
   │   ├── cmnlib64.img
   │   ├── cmnlib.img
   │   ├── devcfg.img
   │   ├── dsp.img
   │   ├── dtbo.img
   │   ├── featenabler.img
   │   ├── hyp.img
   │   ├── imagefv.img
   │   ├── keymaster.img
   │   ├── logo.img
   │   ├── mdm_oem_stanvbk.img
   │   ├── modem.img
   │   ├── multiimgoem.img
   │   ├── odm.img
   │   ├── product.img
   │   ├── qupfw.img
   │   ├── recovery.img
   │   ├── reserve.img
   │   ├── spunvm.img
   │   ├── storsec.img
   │   ├── system_ext.img
   │   ├── system.img
   │   ├── tz.img
   │   ├── uefisecapp.img
   │   ├── vbmeta.img
   │   ├── vbmeta_system.img
   │   ├── vendor.img
   │   ├── xbl_config.img
   │   └── xbl.img
   ├── payload_dumper.py
   ├── README.md
   ├── requirements.txt
   └── update_metadata_pb2.py
   ```

3. 其中在刷`Magisk`比较有用的是`boot.img`及`recovery.img`，可以使用`Magisk`对`boot.img`或`recovery.img`进行修改，修改为`magisk_patch.img`然后对相应分区`flash`

   ```
   $ adb push boot.img /storage/emulated/0/Download/
   # 手机操作`MagiskManager`修改`img`文件
   $ adb pull /storage/emulated/0/Download/magisk_patch.img
   $ adb reboot-fastboot
   # 手机重启至`FastbootMode`
   $ sudo fastboot flash boot magisk_patch.img
   $ sudo fastboot continue # sudo fastboot reboot # 也可
   ```

### 研究一下
```bash
$ adb shell
OnePlus8Pro:/ $ mount
/dev/block/dm-10 on / type ext4 (ro,seclabel,relatime,discard)
tmpfs on /dev type tmpfs (rw,seclabel,nosuid,relatime,size=3837280k,nr_inodes=959320,mode=755)
devpts on /dev/pts type devpts (rw,seclabel,relatime,mode=600,ptmxmode=000)
proc on /proc type proc (rw,relatime,gid=3009,hidepid=2)
sysfs on /sys type sysfs (rw,seclabel,relatime)
selinuxfs on /sys/fs/selinux type selinuxfs (rw,relatime)
tmpfs on /mnt type tmpfs (rw,seclabel,nosuid,nodev,noexec,relatime,size=3837280k,nr_inodes=959320,mode=755,gid=1000)
tmpfs on /mnt/installer type tmpfs (rw,seclabel,nosuid,nodev,noexec,relatime,size=3837280k,nr_inodes=959320,mode=755,gid=1000)
tmpfs on /mnt/androidwritable type tmpfs (rw,seclabel,nosuid,nodev,noexec,relatime,size=3837280k,nr_inodes=959320,mode=755,gid=1000)
/dev/block/sda19 on /metadata type ext4 (rw,sync,seclabel,nosuid,nodev,noatime,discard)
/dev/block/dm-11 on /system_ext type ext4 (ro,seclabel,relatime,discard)
/dev/block/dm-12 on /product type ext4 (ro,seclabel,relatime,discard)
/dev/block/dm-13 on /vendor type ext4 (ro,seclabel,relatime,discard)
/dev/block/dm-14 on /odm type ext4 (ro,seclabel,relatime,discard)
overlay on /system/etc/buildinfo type overlay (ro,context=u:object_r:system_file:s0,relatime,lowerdir=/odm/etc/buildinfo:/system/etc/buildinfo)
tmpfs on /apex type tmpfs (rw,seclabel,nosuid,nodev,noexec,relatime,size=3837280k,nr_inodes=959320,mode=755)
tmpfs on /linkerconfig type tmpfs (rw,seclabel,nosuid,nodev,noexec,relatime,size=3837280k,nr_inodes=959320,mode=755)
none on /dev/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
none on /dev/cg2_bpf type cgroup2 (rw,nosuid,nodev,noexec,relatime)
none on /dev/cpuctl type cgroup (rw,nosuid,nodev,noexec,relatime,cpu)
none on /acct type cgroup (rw,nosuid,nodev,noexec,relatime,cpuacct)
none on /dev/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset,noprefix,release_agent=/sbin/cpuset_release_agent)
none on /dev/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
none on /dev/iolimit type cgroup (rw,nosuid,nodev,noexec,relatime,iolimit)
none on /dev/memcg type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
none on /dev/stune type cgroup (rw,nosuid,nodev,noexec,relatime,schedtune)
tracefs on /sys/kernel/tracing type tracefs (rw,seclabel,relatime)
none on /config type configfs (rw,nosuid,nodev,noexec,relatime)
binder on /dev/binderfs type binder (rw,relatime,max=1048576,stats=global)
none on /sys/fs/fuse/connections type fusectl (rw,relatime)
bpf on /sys/fs/bpf type bpf (rw,nosuid,nodev,noexec,relatime)
pstore on /sys/fs/pstore type pstore (rw,seclabel,nosuid,nodev,noexec,relatime)
none on /sys/fs/cgroup type tmpfs (rw,seclabel,relatime,size=3837280k,nr_inodes=959320,mode=750,gid=1000)
none on /sys/fs/cgroup/freezer type cgroup (rw,relatime,freezer)
/dev/block/sda9 on /mnt/vendor/modemdump type ext4 (rw,seclabel,nosuid,nodev,noatime)
/dev/block/sda2 on /mnt/vendor/persist type ext4 (rw,seclabel,nosuid,nodev,noatime)
/dev/block/sde4 on /vendor/firmware_mnt type vfat (ro,context=u:object_r:firmware_file:s0,relatime,uid=1000,gid=1000,fmask=0337,dmask=0227,codepage=437,iocharset=iso8859-1,shortname=lower,errors=remount-ro)
/dev/block/sde9 on /vendor/dsp type ext4 (ro,seclabel,nosuid,nodev,relatime)
/dev/block/sde5 on /vendor/bt_firmware type vfat (ro,context=u:object_r:bt_firmware_file:s0,relatime,uid=1002,gid=3002,fmask=0337,dmask=0227,codepage=437,iocharset=iso8859-1,shortname=lower,errors=remount-ro)
/dev/block/sda10 on /mnt/vendor/op2 type ext4 (rw,seclabel,nosuid,nodev,noatime,data=ordered)
/dev/block/dm-15 on /data type f2fs (rw,lazytime,seclabel,nosuid,nodev,noatime,background_gc=on,discard,no_heap,user_xattr,inline_xattr,acl,inline_data,inline_dentry,extent_cache,mode=adaptive,active_logs=6,reserve_root=32768,resuid=0,resgid=1065,inlinecrypt,alloc_mode=default,fsync_mode=nobarrier)
/dev/block/loop6 on /system/reserve type ext4 (ro,context=u:object_r:system_file:s0,relatime)
tmpfs on /linkerconfig type tmpfs (rw,seclabel,nosuid,nodev,noexec,relatime,size=3837280k,nr_inodes=959320,mode=755)
/dev/block/dm-15 on /data/user/0 type f2fs (rw,lazytime,seclabel,nosuid,nodev,noatime,background_gc=on,discard,no_heap,user_xattr,inline_xattr,acl,inline_data,inline_dentry,extent_cache,mode=adaptive,active_logs=6,reserve_root=32768,resuid=0,resgid=1065,inlinecrypt,alloc_mode=default,fsync_mode=nobarrier)
tmpfs on /data_mirror type tmpfs (rw,seclabel,nosuid,nodev,noexec,relatime,size=3837280k,nr_inodes=959320,mode=700,gid=1000)
/dev/block/dm-15 on /data_mirror/data_ce/null type f2fs (rw,lazytime,seclabel,nosuid,nodev,noatime,background_gc=on,discard,no_heap,user_xattr,inline_xattr,acl,inline_data,inline_dentry,extent_cache,mode=adaptive,active_logs=6,reserve_root=32768,resuid=0,resgid=1065,inlinecrypt,alloc_mode=default,fsync_mode=nobarrier)
/dev/block/dm-15 on /data_mirror/data_ce/null/0 type f2fs (rw,lazytime,seclabel,nosuid,nodev,noatime,background_gc=on,discard,no_heap,user_xattr,inline_xattr,acl,inline_data,inline_dentry,extent_cache,mode=adaptive,active_logs=6,reserve_root=32768,resuid=0,resgid=1065,inlinecrypt,alloc_mode=default,fsync_mode=nobarrier)
/dev/block/dm-15 on /data_mirror/data_de/null type f2fs (rw,lazytime,seclabel,nosuid,nodev,noatime,background_gc=on,discard,no_heap,user_xattr,inline_xattr,acl,inline_data,inline_dentry,extent_cache,mode=adaptive,active_logs=6,reserve_root=32768,resuid=0,resgid=1065,inlinecrypt,alloc_mode=default,fsync_mode=nobarrier)
/dev/block/dm-15 on /data_mirror/cur_profiles type f2fs (rw,lazytime,seclabel,nosuid,nodev,noatime,background_gc=on,discard,no_heap,user_xattr,inline_xattr,acl,inline_data,inline_dentry,extent_cache,mode=adaptive,active_logs=6,reserve_root=32768,resuid=0,resgid=1065,inlinecrypt,alloc_mode=default,fsync_mode=nobarrier)
/dev/block/dm-16 on /apex/com.android.os.statsd@300901600 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/dm-16 on /apex/com.android.os.statsd type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop8 on /apex/com.android.adbd@300900700 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop8 on /apex/com.android.adbd type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop9 on /apex/com.android.i18n@1 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop9 on /apex/com.android.i18n type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop10 on /apex/com.android.art@1 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop10 on /apex/com.android.art type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop11 on /apex/com.android.tethering@300208500 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop11 on /apex/com.android.tethering type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop12 on /apex/com.android.conscrypt@300900703 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop12 on /apex/com.android.conscrypt type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop13 on /apex/com.android.wifi@300208500 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop13 on /apex/com.android.wifi type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop14 on /apex/com.android.runtime@1 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop14 on /apex/com.android.runtime type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop15 on /apex/com.android.resolv@300208500 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop15 on /apex/com.android.resolv type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop16 on /apex/com.android.tzdata@300900700 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop16 on /apex/com.android.tzdata type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop17 on /apex/com.android.sdkext@300900700 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop17 on /apex/com.android.sdkext type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop18 on /apex/com.android.neuralnetworks@300900700 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop18 on /apex/com.android.neuralnetworks type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop19 on /apex/com.android.ipsec@300900700 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop19 on /apex/com.android.ipsec type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop20 on /apex/com.android.apex.cts.shim@1 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop20 on /apex/com.android.apex.cts.shim type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop21 on /apex/com.android.media.swcodec@300900714 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop21 on /apex/com.android.media.swcodec type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop22 on /apex/com.android.extservices@300900700 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop22 on /apex/com.android.extservices type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop23 on /apex/com.android.mediaprovider@300900729 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop23 on /apex/com.android.mediaprovider type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop24 on /apex/com.android.vndk.v30@1 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop24 on /apex/com.android.vndk.v30 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop25 on /apex/com.android.permission@300208500 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop25 on /apex/com.android.permission type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop26 on /apex/com.android.media@300900700 type ext4 (ro,dirsync,seclabel,nodev,noatime)
/dev/block/loop26 on /apex/com.android.media type ext4 (ro,dirsync,seclabel,nodev,noatime)
tmpfs on /storage type tmpfs (rw,seclabel,nosuid,nodev,noexec,relatime,size=3837280k,nr_inodes=959320,mode=755,gid=1000)
adb on /dev/usb-ffs/adb type functionfs (rw,relatime)
diag on /dev/ffs-diag type functionfs (rw,relatime)
diag_mdm on /dev/ffs-diag-1 type functionfs (rw,relatime)
diag_mdm2 on /dev/ffs-diag-2 type functionfs (rw,relatime)
/data/media on /mnt/runtime/default/emulated type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/runtime/read/emulated type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=23,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/runtime/write/emulated type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=7,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/runtime/full/emulated type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=7,derive_gid,default_normal,unshared_obb)
/dev/fuse on /storage/emulated type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/dev/fuse on /mnt/installer/0/emulated type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/dev/fuse on /mnt/androidwritable/0/emulated type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/dev/fuse on /mnt/user/0/emulated type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/data/media on /mnt/pass_through/0/emulated type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=7,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/androidwritable/0/emulated/0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/0/emulated/0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /storage/emulated/0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/0/emulated/0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/androidwritable/0/emulated/0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /storage/emulated/0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/0/emulated/0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/0/emulated/0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=7,derive_gid,default_normal,unshared_obb)
/dev/fuse on /mnt/installer/999/emulated type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/dev/fuse on /mnt/androidwritable/999/emulated type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/dev/fuse on /mnt/user/999/emulated type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/data/media on /mnt/pass_through/999/emulated type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=7,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/androidwritable/999/emulated/999/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/999/emulated/999/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/999/emulated/999/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/androidwritable/999/emulated/999/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/999/emulated/999/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/pass_through/0/ace-999 type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=7,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/pass_through/999/ace-0 type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=7,derive_gid,default_normal,unshared_obb)
/dev/fuse on /mnt/installer/999/ace-0 type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/dev/fuse on /mnt/androidwritable/999/ace-0 type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/dev/fuse on /mnt/user/999/ace-0 type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/data/media on /mnt/androidwritable/0/emulated/0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/0/emulated/0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /storage/emulated/0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/androidwritable/999/ace-0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/999/ace-0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/0/emulated/0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/999/ace-0/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/androidwritable/0/emulated/0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/0/emulated/0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /storage/emulated/0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/androidwritable/999/ace-0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/999/ace-0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/0/emulated/0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/999/ace-0/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/dev/fuse on /storage/ace-999 type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/dev/fuse on /mnt/installer/0/ace-999 type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/dev/fuse on /mnt/androidwritable/0/ace-999 type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/dev/fuse on /mnt/user/0/ace-999 type fuse (rw,lazytime,nosuid,nodev,noexec,noatime,user_id=0,group_id=0,allow_other)
/data/media on /mnt/androidwritable/999/emulated/999/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/999/emulated/999/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/androidwritable/0/ace-999/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/0/ace-999/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /storage/ace-999/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/999/emulated/999/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/0/ace-999/Android/data type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/androidwritable/999/emulated/999/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/999/emulated/999/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/androidwritable/0/ace-999/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/0/ace-999/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /storage/ace-999/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/999/emulated/999/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/user/0/ace-999/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal,unshared_obb)
/data/media on /mnt/installer/999/emulated/999/Android/obb type sdcardfs (rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=7,derive_gid,default_normal,unshared_obb)
OnePlus8Pro:/ $ df
Filesystem        1K-blocks     Used Available Use% Mounted on
/dev/block/dm-10    1771636  1766260         0 100% /
tmpfs               3837280     1300   3835980   1% /dev
tmpfs               3837280        0   3837280   0% /mnt
/dev/block/dm-11    1095456  1092176         0 100% /system_ext
/dev/block/dm-12     460352   458972         0 100% /product
/dev/block/dm-13    1565912  1561168         0 100% /vendor
/dev/block/dm-14      86616    86356         0 100% /odm
overlay               86616    86356         0 100% /system/etc/buildinfo
tmpfs               3837280        0   3837280   0% /apex
none                3837280        0   3837280   0% /sys/fs/cgroup
/dev/block/sda2       27632     5012     21644  19% /mnt/vendor/persist
/dev/block/dm-15  105515340 36082176  69302092  35% /data
/dev/block/loop6    1606296  1469000    104260  94% /system/reserve
/dev/block/dm-16       1696     1668         0 100% /apex/com.android.os.statsd@300901600
/dev/block/loop8       7728     7700         0 100% /apex/com.android.adbd@300900700
/dev/block/loop9      24640    24608         0 100% /apex/com.android.i18n@1
/dev/block/loop10     81560    81532         0 100% /apex/com.android.art@1
/dev/block/loop11       748      720        16  98% /apex/com.android.tethering@300208500
/dev/block/loop12      4744     4716         0 100% /apex/com.android.conscrypt@300900703
/dev/block/loop13      4564     4536         0 100% /apex/com.android.wifi@300208500
/dev/block/loop14      5116     5080         0 100% /apex/com.android.runtime@1
/dev/block/loop15      2388     2356         0 100% /apex/com.android.resolv@300208500
/dev/block/loop16       844      816        12  99% /apex/com.android.tzdata@300900700
/dev/block/loop17       332      300        28  92% /apex/com.android.sdkext@300900700
/dev/block/loop18      5360     5328         0 100% /apex/com.android.neuralnetworks@300900700
/dev/block/loop19       560      532        20  97% /apex/com.android.ipsec@300900700
/dev/block/loop20       232       92       136  41% /apex/com.android.apex.cts.shim@1
/dev/block/loop21     18144    18116         0 100% /apex/com.android.media.swcodec@300900714
/dev/block/loop22      3988     3960         0 100% /apex/com.android.extservices@300900700
/dev/block/loop23      3884     3856         0 100% /apex/com.android.mediaprovider@300900729
/dev/block/loop24    110756   110724         0 100% /apex/com.android.vndk.v30@1
/dev/block/loop25     18520    18488         0 100% /apex/com.android.permission@300208500
/dev/block/loop26      4488     4460         0 100% /apex/com.android.media@300900700
/dev/fuse         105515340 36082176  69302092  35% /storage/emulated
/dev/fuse         105515340 36082176  69302092  35% /storage/ace-999
$ diff OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/abl.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/abl.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/aop.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/aop.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/bluetooth.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/bluetooth.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/boot.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/boot.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/cmnlib64.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/cmnlib64.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/cmnlib.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/cmnlib.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/devcfg.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/devcfg.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/dsp.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/dsp.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/dtbo.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/dtbo.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/featenabler.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/featenabler.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/hyp.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/hyp.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/imagefv.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/imagefv.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/keymaster.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/keymaster.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/logo.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/logo.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/mdm_oem_stanvbk.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/mdm_oem_stanvbk.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/modem.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/modem.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/multiimgoem.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/multiimgoem.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/odm.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/odm.img differ
Only in OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/: opproduct.img
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/product.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/product.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/qupfw.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/qupfw.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/recovery.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/recovery.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/reserve.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/reserve.img differ
Only in OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output: storsec.img
Only in OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output: system_ext.img
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/system.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/system.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/tz.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/tz.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/uefisecapp.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/uefisecapp.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/vbmeta.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/vbmeta.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/vbmeta_system.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/vbmeta_system.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/vendor.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/vendor.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/xbl_config.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/xbl_config.img differ
Binary files OnePlus8ProHydrogen_15.H.31_OTA_0310_all_2010291922_15e1f3321849475e/payload_dumper/output/xbl.img and OnePlus8ProOxygen_15.E.23_OTA_023_all_2008300442_568e6/payload_dumper/output/xbl.img differ

```


### 后续

1. 在后`/system`分区发现系统装不可删的`APP`，网上说提权后可删，因为`Android11`用`Magisk21.1`卡死了暂时没有继续尝试

2. 查看`img`文件，其实就是`ext`格式文件，在使用`mount system.img system`失败，好像还有一些格式问题，看有些说要用`simg2img`转一下，或者是么改完用`extfstools`重新打回`img`

   > https://github.com/anestisb/android-simg2img
   >
   > https://github.com/nlitsme/extfstools

   ```
   $ file boot.img
   boot.img: Android bootimg, kernel (0x8000), ramdisk (0x1000000), page size: 4096, cmdline (androidboot.hardware=qcom androidboot.console=ttyMSM0 androidboot.memcg=1 lpm_levels.sleep_disa)
   $ file system.img
   system.img: Linux rev 1.0 ext2 filesystem data, UUID=4a50462e-52cf-5fab-87a1-49824cdf6b4f (extents) (large files) (huge files)
   ```

3. 对于其他，好像说华为分区方法不同，刷法也有不同，还有现在`Android`的`boot`分区有用`slot a`及`slot b`可以用`fastboot flash boot_a boot.img`或`flashboot flash boot boot.img --slot all`指定分区或者全刷

4. 还有好些可以玩的，对于各`img`文件解释分析，对`system.img`的修改，各种第三方`ROM`和使用不同的`Kernel`，先做得笔记`# TODO:`一下，之后的未完待续


### 缺陷

1. Android11+Magisk20有严重卡顿无法正常使用

### 参考资料

> https://forum.xda-developers.com/showthread.php?t=2277112
>
> https://forum.xda-developers.com/oneplus-8-pro
>
> https://forum.xda-developers.com/oneplus-8-pro/how-to/guide-how-to-root-oneplus-8-pro-twrp-t4083981
>
> https://magiskmanager.com/
>
>  https://topjohnwu.github.io/Magisk/install.html
>
> https://topjohnwu.github.io/Magisk/install.html#magisk-in-recovery
>
> https://source.android.com/setup/build/running



> 目标周更Blog的第一篇...不过写好了发布脚本了,为周更的懒又...溜了溜了


TODO:

1. OP8P system rollback to android 10 O2 OS
2. install magisk to root
3. install adblock
4. modify system partition to install H2 applications



