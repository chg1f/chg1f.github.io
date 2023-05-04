---
title: "Ssl Disable Connect" # Title of the blog post.
date: 2020-12-28T16:10:20+08:00 # Date of post creation.
# description: "Article description." # Description used for search engine.
# featured: true # Sets if post is a featured post, making appear on the home page side bar.
# menu: main
# featureImage: "/images/path/file.jpg" # Sets featured image on blog post.
# thumbnail: "/images/path/thumbnail.png" # Sets thumbnail image appearing inside card on homepage.
# shareImage: "/images/path/share.png" # Designate a separate image for social media sharing.
# codeMaxLines: 10 # Override global value for how many lines within a code block before auto-collapsing.
# codeLineNumbers: false # Override global value for showing of line numbers within code block.
# figurePositionShow: true # Override global value for showing the figure label.
# comment: false # Disable comment if false.
toc: true # Controls if a table of contents should be generated for first-level links automatically.
categories:
  - bug
tags:
  - ssl
---

**Insert Lead paragraph here.**

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
