---
title: Windows Terminal美化
date: 2022-8-1
updated: 
categories: 美化
tags:
  - Windows Terminal
  - 美化
description: Windows Terminal美化
abbrlink: '0'
---
## Windows Terminal美化 
> 近期看到美化的Windows Terminal就自己想美化一下

### 需要工具列表
> 下列就是所需工具的链接
- [Windows Terminal](https://github.com/microsoft/terminal)（微软商店可搜索下载）
- [oh my posh 一个适用于任何shell的提示主题引擎](https://ohmyposh.dev/)
- [nerd fonts 字体下载](https://www.nerdfonts.com/font-downloads)

## 配置步骤
### 首先安装Windows Terminal
这一步就不说明了，按步骤安装即可（注意：需要Win10或Win11系统）

### 打开Windows Terminal json配置文件
1. 打开设置
![](https://cos.blog.fsyume.com/blog-pic/202208021123977.png)
2. 打开JSON文件
![](https://cos.blog.fsyume.com/blog-pic/202208021125412.png)
3. 配置JSON文件
> 如果不想自己配置
> 这个网站提供现成的模板：
> https://windowsterminalthemes.dev/

找到如同所示的JSON段
![](https://cos.blog.fsyume.com/blog-pic/202208021130726.png)

### 配置文件参数
**配置参数说明（部分）**
```json
{
    "useAcrylic":true, //毛玻璃效果 
    "acrylicOpacity": 1, // 不透明度0-1
    "backgroundImage":"C:\\Users\\Chihying\\OneDrive\\终端BI.png", // 背景图片地址，推荐暗色图片，避免影响字体显示
    "backgroundImageOpacity":0.2, // 背景图片的不透明度
    "fontFace": "JetBrainsMono Nerd Font Mono" // 字体
}

```

### 安装oh my posh
我们可以直接在powershell中输入下面的命令一键安装
```shell
winget install JanDeDobbeleer.OhMyPosh -s winget
```
也可以根据官网的操作，进入商店进行下载
![](https://cos.blog.fsyume.com/blog-pic/202208021137625.png)

### 配置oh my posh
安装完毕之后我们需要配置一下脚本
打开powershell，输入：
```shell
 code $PROFILE
```
通过vs code启动该文件
输入以下配置，并保存
```shell
oh-my-posh init pwsh --config $env:POSH_THEMES_PATH\montys.omp.json | Invoke-Expression
```
**这样就配置完毕了，剩下就是选择自己喜欢的字体即可（注意官方强调为了图标显示必须Nerd字体）**
![](https://cos.blog.fsyume.com/blog-pic/202208021142259.png)
在[nerdfonts.com](https://www.nerdfonts.com/font-downloads) 选择自己喜欢的Nerd字体即可

