---
title: 通过Python和BilibiliAPI来获取B站账户UID和粉丝数
date: 2020-8-1
updated: 2020-8-1
categories: Python
tags:
  - Python
  - 小程序
description: 通过Python和BilibiliAPI来获取B站账户UID和粉丝数
abbrlink: 405f
sticky:
---

### 需要工具：

- Python的requests库
- [Bilibili用户信息API（点我进入bilibiliAPI合集）](https://www.bilibili.com/read/cv3430609?from=search)

> Bilibili用户API

![](https://gitee.com/kzycn/picCloud/raw/master/2020/20200910135717.png)

```python
bili_api = 'https://api.bilibili.com/x/relation/stat?vmid=115505904'
```

通过requests库的get方法来请求API来获取json数据

![](https://gitee.com/kzycn/picCloud/raw/master/2020/20200910140603.png)

```python
# json,get请求模块
def jsonget():
    http_data = requests.get(url=bili_api).json()
    data = http_data["data"]
    return data
```

获取data数组，返回data

data里面的following是粉丝数据

将follower数据传给fans，返回fans，得到粉丝数

```python
# 获取粉丝数模块
def get_fans():
    fans = jsonget()["follower"]
    return fans
```

同理，也能获得用户UID

```python
# 获取用户UID
def get_uid():
    bili_uid = jsonget()["mid"]
    return bili_uid
```

将两个模块放到主模块

```python
if __name__ == '__main__':
    main()

def main():
    print('目前粉丝数:', get_fans())
    print('用户uid:', get_uid())
```





