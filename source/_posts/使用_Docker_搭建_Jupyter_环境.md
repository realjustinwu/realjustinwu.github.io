---
title: 使用 Docker 搭建 Jupyter 环境
date: 2019-03-07 21:54
tags: [Docker, Jupyter]
categories: 
blog: true
---



刚刚学完 Docker 容器，小试一下牛刀，使用 Docker 搭建一个 Jupyter 环境。

只需要三步就能完成环境搭建

1. 安装 Docker（网上一堆资料）
2. 下载配置文件
3. 启动创建并启动容器



<!-- more -->

## 1. 安装 Docker

(略)

## 2. 下载配置文件

`git clone https://github.com/binwin20/Docker-Jupyter.git`

## 3. 启动创建并启动容器

```sh
cd Docker-Jupyter
docker-compose up -d
```

首次启动时间有点长，需要下载 continuumio/anaconda3 镜像，然后安装 nbextension 插件。启动之后，打开 `http://localhost:8888` 就可以看到 Jupyter 页面了

{% img "/images/jupyter.png" %}

## 常用命令

```sh
# 创建并启动容器并放后台
docker-compose up -d
# 停止容器
docker-compose stop
# 再次启动容器
docker-compose start
# 重启容器
docker-compose restart
```
