---
title: maven配置
date: 2020-9-17
updated: 2020-9-17
categories:
  - Java
tags: Maven
keywords: 'Java,Maven'
description: maven配置
abbrlink: f520
sticky:
---

### maven下载

[maven下载地址](https://maven.apache.org/download.cgi)

下载后解压，在文件夹内创建新文件夹，`maven-repo`，作为maven仓库
<!--more-->
![](https://gitee.com/kzycn/picCloud/raw/master/2020/image-20200922204757806.png)

找到`setting.xml`文件，用VS code或者notepad++打开

![](https://gitee.com/kzycn/picCloud/raw/master/image-20200922204848254.png)

添加仓库绝对地址

![](https://gitee.com/kzycn/picCloud/raw/master/2020/image-20200922205048790.png)

由于maven仓库在国外，所有我们需要设置国内镜像

![](https://gitee.com/kzycn/picCloud/raw/master/2020/image-20200922205229284.png)

```MAVEN
<mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
  　　<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
```

就此maven配置完毕