---
title: OpenCV图像识别
date: 2023-1-1
categories: Python
tags:
  - OpenCV
  - Python
  - 图像识别
description: OpenCV图像识别
abbrlink: 83ee8574
updated:
---

## 前置准备

### 环境介绍

- Windows 10 Pro
- Python 3.11
- OpenCV 4.7.0.68

### OpenCV库

> 以下内容来自维基百科	2023年1月1日

**OpenCV**的全称是Open Source Computer Vision Library，是一个跨平台的[计算机视觉](https://zh.m.wikipedia.org/wiki/计算机视觉)库。OpenCV是由[英特尔公司](https://zh.m.wikipedia.org/wiki/英特尔)发起并参与开发，以[BSD许可证](https://zh.m.wikipedia.org/wiki/BSD许可证)授权发行，可以在商业和研究领域中免费使用。OpenCV可用于开发实时的[图像处理](https://zh.m.wikipedia.org/wiki/图像处理)、[计算机视觉](https://zh.m.wikipedia.org/wiki/计算机视觉)以及[模式识别](https://zh.m.wikipedia.org/wiki/模式识别)程序。该程序库也可以使用英特尔公司的[IPP](https://zh.m.wikipedia.org/w/index.php?title=集成性能基元&action=edit&redlink=1)进行加速处理。

## 主要API

### `cv2.cvtColor(src, code[, dst[, dstCn]])`函数

用于将图像从一种颜色空间转换为另一种颜色空间。

```python
cv2.cvtColor(src, code[, dst[, dstCn]])

参数：
	src: 它是要更改其色彩空间的图像。
	code: 它是色彩空间转换代码。
	dst: 它是与src图像大小和深度相同的输出图像。它是一个可选参数。
	dstCn: 它是目标图像中的频道数。如果参数为0，则通道数自动从src和代码得出。它是一个可选参数。

返回值：它返回一个图像。
```

### `cv2.CascadeClassifier(xml)`函数

联级分类器目标检测

```python
cv2.CascadeClassifier(xml)

参数：
	xml: haar人脸特征分类器 OpenCV源代码中提供有
```

### 主要代码

```python
import cv2 as cv

def face_detect_demo():
    # 转换灰度
    gary = cv.cvtColor(img, cv.COLOR_BGR2GRAY)

    # 分类器（OpenCV基于 Haar 级联的人脸检测器）
    face_detect = cv.CascadeClassifier("haarcascade_frontalface_alt2.xml")

    face = face_detect.detectMultiScale(gary)

    # 绘制方框
    for x, y, w, h in face:
        cv.rectangle(img, (x, y), (x + w, y + h), color=(0, 0, 255), thickness=2)

    # 展示图片
    cv.imshow('re_img', img)

# 读取图片
img = cv.imread("./pic/my.png")

face_detect_demo()

while True:
    if ord('q') == cv.waitKey(0):
        break

cv.destroyAllWindows()
```



