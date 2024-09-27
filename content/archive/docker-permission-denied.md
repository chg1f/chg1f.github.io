---
title: "Docker有权限但权限不足"
date: 2019-12-09
categories:
- bug
tags:
- docker
---

## 问题/Problem

1. Docker容器中已分配權限,但無法訪問文件

## 原因/Reason

1. Selinux限制

## 解决/Fix

1. Selinux關閉或添加權限

