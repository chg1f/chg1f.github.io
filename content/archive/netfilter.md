---
title: "Netfilter"
Description: "Netfilter"
tags:
- linux
- network
- netfilter
- kernel
draft: true
---

# Netfilter

## Intro

`iptables`, `ebtable`, ..

## HOOK

- `NF_IP_PRE_ROUTING`
- `NF_IP_LOCAL_IN`
- `NF_IP_FORWARD`
- `NF_IP_LOCAL_OUT`
- `NF_IP_POST_ROUTING`

## Chain

- `PREROUTING`
- `INPUT`
- `FORWARD`
- `OUTPUT`
- `POSTROUTING`

## Table

- `FILTER`
- `NAT`
- `MANGLE`
- `RAW`
- `SECURITY`

## Priority

- GetAndLocal
  1. `PREROUTING`
  1. `INPUT`
- GetAndOther
  1. `PREROUTING`
  1. `FORWARD`
  1. `POSTROUTING`
- Produce
  1. `OUTPUT`
  1. `POSTROUTING`
