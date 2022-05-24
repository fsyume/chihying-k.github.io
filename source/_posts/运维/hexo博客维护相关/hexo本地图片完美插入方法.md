---
title: hexo本地图片完美插入方法
categories: 其他
tag: 
    - hexo维护
abbrlink: faae
date: 2022-03-25 21:44:24
tags:
---

> 摸鱼时候发现gitee把我图片仓库给ban了（真无语），就突然有hexo本地图片的需求

<!--more-->

**所需插件**

- `hexo-asset-img`🍰 Hexo 本地图片插件: 转换 图片相对路径 为 asset_img

## 使用

```bash
npm install hexo-asset-img --save

hexo-typora
├── apppicker.jpg
├── logo.png
└── rules.jpg
hexo-typora.md
```

Make sure `post_asset_folder: true` in your `_config.yml`.

`hexo-typora.md`: Just use `![logo](hexo-typora/logo.png)` to insert `logo.png`.

### 与 Typora 配合使用（实测MarkTest也能完美配合）

- [Hexo + Typora + 开发Hexo插件 解决图片路径不一致 | yiyun's Blog](https://moeci.com/posts/hexo-typora)

**感谢[@yiyun](https://github.com/yiyungent)编写的插件**

> 图片演示👇（FF14自家猫猫@银泪湖）

![](hexo本地图片完美插入方法/8bc32dc32d6ce0e5dab4d52b714c0f3a7f72ae03.webp)
