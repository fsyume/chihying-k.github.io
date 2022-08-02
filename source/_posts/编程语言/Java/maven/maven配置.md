---
title: maven配置
date: 2020-9-17
updated: 2020-9-17
categories: Java
tags: 
  - 编程语言
  - Java
  - Maven
keywords: 'Java,Maven'
description: maven配置
abbrlink: f520
---

## maven下载

[maven下载地址](https://maven.apache.org/download.cgi)

下载后解压，在文件夹内创建新文件夹，`maven-repo`，作为maven仓库
<!--more-->
![](https://cos.blog.fsyume.com/blog-pic/202205261335107.png)

找到`setting.xml`文件，用VS code或者notepad++打开

![](https://cos.blog.fsyume.com/blog-pic/202205261336485.png)

添加仓库绝对地址

![](https://cos.blog.fsyume.com/blog-pic/202205261336261.png)

由于maven仓库在国外，所有我们需要设置**国内镜像**

> 阿里云Maven镜像仓库地址：https://developer.aliyun.com/mvn/guide

![](https://cos.blog.fsyume.com/blog-pic/202205261337469.png)

```xml
<mirror>
      <id>aliyunmaven</id>
      <mirrorOf>*</mirrorOf>
      <name>阿里云公共仓库</name>
      <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

就此maven配置完毕