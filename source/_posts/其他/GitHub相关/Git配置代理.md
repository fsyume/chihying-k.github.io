---
title: 为本地git配置代理
categories: git
tags:
  - git
  - GitHub
  - 代理
  - SSH
description: 为本地git配置代理
abbrlink: 1c4b584d
date: 2024-04-27 00:00:00
updated: 
sticky:
---

# 为本地git配置代理

```shell

git config --global https.proxy socks5://127.0.0.1:7890

git config --global https.proxy socks5://127.0.0.1:7890

git config --global --unset http.proxy

git config --global --unset https.proxy

```