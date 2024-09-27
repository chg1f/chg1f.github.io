---
title: "base64在Windows及Linux下错误"
date: 2019-12-09
categories:
- bug
tags:
- linux
- windows
- base64
---

## 问题/Problem

1. 在linux上`echo "STRING"|base`時與decode結果不一致,在python base64.b64encode下正常

## 原因/Reason

1. 因為首先關于換行符, echo會打印換行,在Windows下為\r\n,在*nix為\n,在早期Mac下為\r,因為這個引至第一個BUG點
2. echo 可以用-n消行echo出來自身的換行,用-e可以解釋字符中的替換符

## 解决/Fix

1. `echo -en "STRING\r\n"|base64`

