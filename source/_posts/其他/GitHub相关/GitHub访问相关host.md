---
title: Github访问问题
date: 2021-3-14
updated: 2021-3-14
categories: 
    - GitHub
tags: GitHub
keywords: GitHub
description: Github访问问题解决方法
abbrlink: 79cb
sticky:
---

# Github访问问题

> 最近GitHub一直抽风，在学校大部分时间根本访问不了，查找问题原因，怀疑是DNS污染

在Gitee找到个项目可以解决
<!--more-->
[项目地址](https://gitee.com/jyotish/githubhosts)

里面保存有GitHub真实的地址，修改系统host文件即可

```hosts
199.232.69.194 github.global.ssl.fastly.net
140.82.112.4 www.github.com
185.199.108.153 assets-cdn.github.com
185.199.109.153 assets-cdn.github.com
185.199.110.153 assets-cdn.github.com
185.199.111.153 assets-cdn.github.com
185.199.108.153 documentcloud.github.com
185.199.109.153 documentcloud.github.com
185.199.110.153 documentcloud.github.com
185.199.111.153 documentcloud.github.com
140.82.114.3 gist.github.com
185.199.108.153 help.github.com
185.199.109.153 help.github.com
185.199.110.153 help.github.com
185.199.111.153 help.github.com
140.82.112.9 nodeload.github.com
199.232.68.133 raw.github.com
140.82.112.18 status.github.com
140.82.113.18 training.github.com
199.232.68.133 raw.githubusercontent.com
199.232.68.133 user-images.githubusercontent.com
199.232.68.133 avatars1.githubusercontent.com
199.232.68.133 avatars2.githubusercontent.com
199.232.68.133 avatars3.githubusercontent.com
199.232.68.133 cloud.githubusercontent.com
140.82.113.6 api.github.com
```

#### 改完如何刷新生效

**Windows**

```
ipconfig /flushdns
```

**Ubuntu**

```
sudo systemctl restart nscd
```

**Mac**

```
sudo killall -HUP mDNSResponder
```

