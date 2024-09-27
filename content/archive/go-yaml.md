---
title: "GO解释YAML错误"
date: 2019-11-06
categories:
- bug
tags:
- golang
- yaml
---

## 问题/Problem

1. TODO

## 原因/Reason

1. 因为解释调用了第三方包，但解释结构体中的属性为非公开属性，第三方包无法读职这些字段

## 解决/Fix

1. 结构体中需解释字段修改为公开

