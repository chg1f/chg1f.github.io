---
title: "Ngrok Tunnel No Found"
date: 2019-11-05
categories:
- bug
tags:
- ngrok
- dns
---

## 问题/Problem

1. TODO

## 原因/Reason

1. ngrokd服務器無法路由請求,在ngrok1.X中subdomain為三級域名,DNS需設置三級域名解析到ngrokd服務器,或使用hostname重寫為二級域名

## 解决/Fix

1. 解決路由問題
2. godday dns的三級域名解析方法為主机: *.*

