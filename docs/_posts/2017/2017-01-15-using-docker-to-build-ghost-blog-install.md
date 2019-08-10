---
layout: post
class: post-template
navigation: True
title: 使用Docker构建Ghost博客(1/2)：安装
date: '2017-01-15 11:33:34'
tags:
- docker
- ghost-blog
---

Ghost是一款年轻而轻量的博客系统，使用Node.js技术实现。相比WordPress的大而全，Ghost要清新的多，后台简洁，并提供了完整的Markdown编辑器进行博客创作。
总结来说，Ghost的优点：

* 可使用轻量的SQLite作为数据库，对于小型博客，维护起来更方便（同时也支持MySql）
* 完全的Markdown编辑器支持

对于独立部署博客系统，需要考虑备份和迁移的成本。使用Docker可以最大程度的降低部署复杂性，进而提高备份和迁移的便捷性。
本文将从介绍使用Docker快速部署、备份和切换Ghost博客。

# 安装Docker
参考：

# Docker安装Ghost博客
## 下载Ghost的Docker镜像
执行命令`docker pull ghost`，拉取最新的Ghost官方镜像，成功后执行`docker images`查看到已拉取的镜像：

![docker images](http://ok4jsyu7n.bkt.clouddn.comhttps://cdn.imshuai.com/images/2017/01/-----2017-01-15-19.09.45.png)

## 启动容器
启动容器需要注意以下几点：

* Ghost默认在2368端口开放http服务，我们使用Docker的端口映射，将其映射到80端口。
* 容器内Ghost的配置目录是/var/lib/ghost，我们映射到本地文件系统，便于备份
* 为容器取一个名字，便于后续管理

为此，我们使用如下命令启动Ghost博客：

`docker run --name ghost_80 -d -p 80:2368 -v /root/ghost/ghost_config:/var/lib/ghost ghost`

运行的容器名称是ghost_80，映射到本机80端口，并将配置目录映射到本机的/root/ghost/ghost_config

执行`docker ps`查看：

![docker ps](http://ok4jsyu7n.bkt.clouddn.comhttps://cdn.imshuai.com/images/2017/01/-----2017-01-15-19.16.11.png)

查看本机的/root/ghost/ghost_config目录，可以看到自动生成了配置文件：
![ghost config](http://ok4jsyu7n.bkt.clouddn.comhttps://cdn.imshuai.com/images/2017/01/-----2017-01-15-19.18.57.png)

# Ghost后台
安装完毕后，在浏览器里运行查看，可以看到Ghost的默认首页。url后添加/ghost，进入后台管理界面，首次进入需要创建博客账户：

![Ghost Register](http://ok4jsyu7n.bkt.clouddn.comhttps://cdn.imshuai.com/images/2017/01/-----2017-01-15-19.24.07.png)

进入后台，界面非常简洁：
![Ghost Admin Panel](http://ok4jsyu7n.bkt.clouddn.comhttps://cdn.imshuai.com/images/2017/01/-----2017-01-15-19.26.48.png)
阅读默认的“Welcome to Ghost”，学习一下基本的Markdown语法，即可以开始Ghost博客之旅。

# 其他注意

## 设置博客的url地址
容器默认配置的url地址是localhost:2368，部署到服务器后，会导致某些链接不可用。需要到Ghost的配置目录，修改config.js文件development节点（Ghost默认运行在development环境），修改url为正确的地址：
![](http://ok4jsyu7n.bkt.clouddn.comhttps://cdn.imshuai.com/images/2017/01/-----2017-01-15-19.30.36.png)

## 时区设置
对于部署在海外服务器上的用户来说，时区可能不正确，到Ghost的后台Panel点击General页签可以设置
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzI5MTI0MDE0XX0=
-->
