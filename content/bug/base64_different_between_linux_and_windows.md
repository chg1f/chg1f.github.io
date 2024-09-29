---
title: "Base64 different between linux and windows"
date: 2019-12-09
tags: ["bug"]
---

## Question

- in Linux, `echo "STRING"|base64` is different from python `base64.b64encode` result.

## Reason

- echo will print newline, in Windows is \r\n, in *nix is \n, in early Mac is \r, this lead to the first bug.

## Fixed

- `echo -en "STRING\r\n"|base64`
