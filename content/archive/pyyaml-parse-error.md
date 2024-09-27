---
title: "PyYaml解释‘21:21`错误"
date: 2019-12-09
categories:
- bug
tags:
- yaml
- docker-compose
---

## 问题/Problem

1. 在docker-compose.yml中有
   ```yaml
   - 21:21
   ```

   解析解果為`int(1281)`非`str("21:21")`

## 原因/Reason

1. 在python中pyyaml解釋中發現`21:21`會解釋為時間類型,再轉為秒數輸出

   > `X:Y` X為小時,Y為分鐘

## 解决/Fix

1. 在yaml文件中指定為字符串
   ```yaml
    - "21:21"
   ```

