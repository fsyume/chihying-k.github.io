---
title: Docker
categories:
  - 运维
  - Docker
tags:
  - Docker安装部署
  - Docker
keywords: 'Docker,安装,部署'
description: Docker安装&部署+常用指令
abbrlink: a01f
date: 2020-12-12 00:00:00
updated: 2020-12-12 00:00:00
sticky:
---

## Docker安装

[Docker官方文档](https://docs.docker.com/get-docker/)

## CentOS下安装

### 更新yum包

<!--more-->
```shell
sudo yum update
```

### 安装需要的软件包

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
```

### 卸载旧版本

```shell
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```



### yum安装

```shell
sudo yum install -y yum-utils
```

### 配置镜像加速

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://fimadrya.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 安装docker engine

```shell
sudo yum install docker-ce docker-ce-cli containerd.io
```

### 启动docker

```shell
sudo systemctl start docker
```

### 测试

```shell
sudo docker run hello-world
```

# Docker常用命令

## 帮助命令

```shell
docker version #docker版本信息
docker info #详细信息
docker 命令 --help #万能命令
```

docker官方参考文档：https://docs.docker.com/reference/

## 镜像命令

**docker images**查看所有本地主机上的镜像

```shell
[root@iZm5e1u64hvs1mdn0hhqztZ ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        11 months ago       13.3kB

# 解释
REPOSITORY	镜像的仓库源
TAG			镜像的标签
IMAGE ID	镜像的ID
CREATED		镜像的创建时间
SIZE		镜像的大小

# 可选项
Options:
  -a, --all             列出的所有的镜像
  -q, --quiet           只显示镜像的id

```

**docker search**搜索镜像

```shell
docker search 要搜索的镜像
```

**docker pull**下载mysql

```shell
[root@iZm5e1u64hvs1mdn0hhqztZ ~]# docker pull mysql
Using default tag: latest	#如果不写tag，默认latest，最后一个版本
latest: Pulling from library/mysql
852e50cd189d: Pull complete 	#分层下载，docker images的核心 联合文件系统
29969ddb0ffb: Pull complete 
a43f41a44c48: Pull complete 
5cdd802543a3: Pull complete 
b79b040de953: Pull complete 
938c64119969: Pull complete 
7689ec51a0d9: Pull complete 
a880ba7c411f: Pull complete 
984f656ec6ca: Pull complete 
9f497bce458a: Pull complete 
b9940f97694b: Pull complete 
2f069358dc96: Pull complete 
Digest: sha256:4bb2e81a40e9d0d59bd8e3dc2ba5e1f2197696f6de39a91e90798dd27299b093#签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest# 真实地址

# 指定版本下载
docker pull mysql:5.7
```

**docker rmi**删除镜像

## 容器命令

> 说明：有了镜像才能创建容器

```shell
docker pull centos
```

**新建容器并启动**

```shell
docker run [可选参数] image

# 参数说明
--name="Name"	容器名字
-d				后台方式运行
-it				交互方式运行，进入容器查看内容
-p				指定容器端口
	-p 主机端口:容器端口（常用）
-P				随机指定端口（大写P）
```

**测试**

```shell
[root@iZm5e1u64hvs1mdn0hhqztZ ~]# docker run -it centos
[root@e85283b1056e /]# 

# 容器内就是一个centos系统
```

**退出容器**

```shell
exit #关闭并退出 从容器中退回主机
Ctrl+p+q #容器不停止退出
```

**列出所有的运行的容器**

```shell
docker ps

-a #列出当前正在运行的容器+带出历史运行过的容器
-n=? #显示最近创建的容器
-q #只显示容器的编号
```

**删除容器**

```shell
docker rm 容器id
```

**启动和停止容器的操作**

```shell
docker start 容器id	#启动容器
docker restart 容器id	#重启容器
docker stop 容器id	#停止当前正在运行的容器
docker kill 容器id	#停止当前正在运行的容器
```

## 常用其他命令

**后台启动容器**

```shell
# 命令 docker run -d 镜名

# 问题，发现centos停止了
# 常见的坑：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会停止
```

**查看日志**

```shell
docker logs --help
```

**查看容器中的进程**

```shell
docker top 容器id
```

**进入当前正在运行的容器**

```shell
docker exec -it 容器id bashShell
```

