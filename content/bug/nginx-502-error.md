---
title: "Nginx502错误"
date: 2019-12-09
categories:
- bug
tags:
- nginx
---

## 问题/Problem

1. nginx 在轉發中報錯日志為
   `... upstream sent too big header while reading response header from upstream ...`

## 原因/Reason

1. 反向代理的源站HTTP頭太大了

   > `proxy_buffer_size`
   > `proxy_buffers`
   > `proxy_busy_buffers_size` 
   >
   > http://nginx.org/en/docs/http/ngx_http_proxy_module.html

## 解决/Fix

```nginx
location \ {
    proxy_buffer_size 4k;
    proxy_buffers 4 256k;
    proxy_busy_buffers_size 256k;
}
```

