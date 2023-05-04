---
title: "How To Root Android"
description: "安卓手机获取ROOT权限"
---

0.
> https://source.android.com/setup/build/running
```bash
adb devi
adb reboot
```

1. Unlocking Bootloader
> https://source.android.com/devices/bootloader/locking_unlocking
```bash
fastboot flashing unlock
```

2. Flashing boot partition

bootloader
root permission

recovery
fastboot
adb


- 如何从OTA升级包获取boot.img
```bash
$ unzip {OTA package}
$ cd {OTA package}
$ git clone https://github.com/vm03/payload_dumper.git
$ pip install -r payload_dumper/requirements.txt
$ python payload_dumper/payload_dumper.py payload.bin
$ file output/boot.img
```

- magisk
> https://github.com/topjohnwu/Magisk
- edxposed
> https://github.com/ElderDrivers/EdXposed
