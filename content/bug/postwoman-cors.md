---
title: "Postwoman CORS错误"
date: 2020-04-06
categories:
- bug
tags:
- postwoman
---

## 问题/Problem

1. postwoman发送请求一直出现CORS，使用了postwoman extensions后只能在postwoman.io中成功，自建失败

## 原因/Reason

1. 浏览器安全策略，引至CORS
2. 原生postwoman extensions只能对postwoman官方项目生效

```
      "matches": [
        "https://postwoman.io/",
        "https://postwoman.io/*",
        "https://postwoman.netlify.com/*",
        "https://postwoman.netlify.com/"
      ],
```

## 解决/Fix

1. 修改postwoman extensions，对自建增加支持

