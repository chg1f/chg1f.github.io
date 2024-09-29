---
title: "Ssl Disable Connect"
date: 2020-12-28T16:10:20+08:00
tags: ["ssl"]
---

## Question
```bash
$ curl https://...
curl: (35) error:1425F102:SSL routines:ssl_choose_client_version:unsupported protocol
$ python with_ssl_session.py
[DEBUG] SSL connection error occurred ('[SSL: WRONG_SSL_VERSION] wrong ssl version (_ssl.c:727)')
[DEBUG] SSL connection error occurred ('[Errno 0] Error')
[WARNING] can't establish SSL connection
```
## Reason

## Fixed
```bash
$ vim /etc/ssl/openssl.cnf
```
disable protocol minimum version constraints
```
[system_default_sect]
# MinProtocol = TLSv1.2
```
