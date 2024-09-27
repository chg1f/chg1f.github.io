---
title: "ADB Device No Permissions"
date: 2020-12-09T13:06:13+08:00
tags: ["bug", "adb", "android"]
---


## Question

```bash
adb devices
# List of devices attached
# 00000000        no permissions (user in plugdev group; are your udev rules wrong?); see [http://developer.android.com/tools/device.html]
```

## Fixed

```bash
sudo adb kill-server
sudo pkill adb
sudo adb start-server
adb devices
# List of devices attached
# 00000000        unauthorized
```
