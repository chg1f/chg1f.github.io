---
title: "Protobuf在C/CPP下字符串截断"
date: 2019-11-12
categories:
- bug
tags:
- protobuf
---

## 问题/Problem

1. 程序在使用gRPC,protobuf時因為字符串中出現`\0`發現截斷現象

## 原因/Reason

1. `\0`為C及C++中的字符串結束符
   protobuf中的byte,string等連續內存型數據類型在C/CPP都是以string為轉換類型,無法只修改自身代碼FIX
   > [proto2](https://developers.google.com/protocol-buffers/docs/proto#scalar)
   > [proto3](https://developers.google.com/protocol-buffers/docs/proto3#scalar)

## 解决/Fix

1. 因為不存在其他連續內存型數據類型,只能對字符串進行處理,把`\0`隱去,在最后轉換回來,且自身C/CPP處理中也不能使用string類型處理己轉換類型,要使用字符流
2. 原想用hex編碼修改,有人提醒想起用base64更好,壓縮能力..
