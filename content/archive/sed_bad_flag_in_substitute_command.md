---
title: "sed bad flag in substitute command"
description: ""
date: 2021-07-16T10:31:08+08:00
tags:
- sed
- cli
- macOS
---

## Environment

```bash
$ sw_vers
ProductName:    macOS
ProductVersion: 11.4
BuildVersion:   20F71
```

## Question

```
$ echo "abcdefg" > test
$ sed "s/b/d/g" test
adcdefg
$ sed -i "s/b/d/g" test
... error
# undefined label
# bad flag in substiute command
```

## Debugging

## Fixed

```bash
$ sed -i.bak "s/b/d/g" test
$ gsed -i "s/b/d/g" test
```
