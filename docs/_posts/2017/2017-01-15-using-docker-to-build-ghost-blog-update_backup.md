---
layout: postclass: post-templatenavigation: True
title: 使用Docker构建Ghost博客(2/2)：备份和更新
date: '2017-01-15 11:45:00'
tags:
- docker
- ghost-blog
---

上一篇[使用Docker构建Ghost博客(1/2)：安装](/using-docker-to-build-ghost-blog-install/)，我们通过Docker已经完成了Ghost博客系统的安装，本文将介绍如何对Ghost博客进行更新和备份。

### 1.备份Ghost博客
#### 1.1 手动备份
部署Ghost的时候，很重要的一点是：将容器中Ghost的配置目录映射到本机目录。这样在更新Ghost镜像的时候，博客的数据和配置可以直接保留。
因此，我们只用备份当时映射的博客的配置目录即可。由于，我们使用的是SQLite数据库，数据库的备份十分简单，只用备份db文件即可。

1. 停止容器。
`Docker stop my_ghost`，停止运行Ghost的Docker容器即可。（my_ghost是容器名）
1. 备份Ghost配置目录。
`tar -zcvf ghost_config.tar.gz ghost_config`，
将配置目录压缩打包到一个tar.gz文件里
1. 启动容器
`docker start my_ghost`，恢复博客访问
#### 1.2 自动备份
当然，可以做成一个脚本，并部署到服务器的crontab里定期自动执行
 
```bash
# 容器名称（修改为自己的容器名称）
dockerName=ghost_0.11.4
# 备份的配置文件目录
ghostConfigPath=/root/ghost/ghost_config
# 备份文件.tar.gz的存储路径
backupPath=/root/ghost/backup
# 备份文件时间戳后缀
timeStamp=`date +%Y%m%d%H%M%S`
docker stop $dockerName
cd $backupPath
tar -zcvf ghost_backup_${timeStamp}.tar.gz $ghostConfigPath
docker start $dockerName
```   
然后将这个脚本部署在服务器上，通过如下crontab命令做到每日凌晨3:00备份

    0 3 * * * /root/ghost/backup/backup_ghost.sh
以后若要备份的时候，直接执行这个脚本即可。

### 2. 更新Ghost博客
Ghost博客程序更新版本后，也会同时发布新版本Ghost的Docker镜像。因此，==Ghost博客的更新问题，就完全变成了更新Docker镜像的问题。==

1. 先做备份
未避免任何意外，建议先做备份。
1. 拉取最新的镜像
使用`docker pull ghost`，拉取最新的镜像。
1. 停止当前容器
`docker stop my_ghost`
1. 删除当前容器
`docker rm my_ghost`
1. 使用最新镜像，启动一个新的容器
`docker run --name my_ghost -d -p my_ghost 80:2368 -v /root/ghost/ghost_config:/var/lib/ghost ghost`

### 3. 总结
1. 使用Docker，屏蔽了对特定程序的**管理差异性**。转化为一致的Docker镜像的安装和升级操作。比如Ghost内部的安装和升级方法，几乎变成透明的。
1. 将状态数据保存到容器之外，做到容器本身无状态，方便容器升级。
