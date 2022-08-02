---
title: 同时将代码同步到GitHub和Gitee
date: 2021-3-14
updated: 2021-3-14
categories: GitHub
tags:
  - GitHub
  - 代码同步
  - SSH
description: 同时将代码同步到GitHub和Gitee
abbrlink: f5ef
sticky:
---

# 同时将代码同步到GitHub和Gitee

> 最近想把项目同时在两个平台更新，就开始摸索怎么将代码同步到两个平台

### 首先配置本地的ssh密钥
<!--more-->
1. 打开CMD
2. 输入命令`ssh-keygen -t rsa`生成公钥和私钥

### 打开公钥和私钥的存放地点

Windows系统默认在

```
C:\Users\<用户名>\.ssh
```

其中id_rsa.pub是公钥

用记事本或者vscode打开后全部复制

### 打开Gitee和Github的SSH公钥（在个人设置里面）

复制并保存

### 乌龟git（TortoiseGit）相关设置

如果你使用的是乌龟git可视化操作需要配置乌龟git的默认ssh

![乌龟git](https://cdn.jsdelivr.net/gh/kzycn/cdn/Github/TortoiseGit.png)

