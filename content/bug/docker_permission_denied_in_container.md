---
title: "Docker permission denied in container"
date: 2019-12-09
tags: ["bug", docker"]
---

## Question

1. Accessing files in Docker container with permission denied, even though permission is granted.

## Reason

1. Selinux restriction

## Fixed

1. Disable Selinux or add permission
