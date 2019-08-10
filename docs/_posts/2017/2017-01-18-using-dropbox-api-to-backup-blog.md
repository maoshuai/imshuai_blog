---
layout: postclass: post-templatenavigation: True
title: 使用Dropbox API备份Ghost博客
date: '2017-01-18 14:10:39'
tags:
- backup
- dropbox
- ghost-blog
---

在之前的文章，[《使用Docker构建Ghost博客(2/2)：备份和更新》](/using-docker-to-build-ghost-blog-update_backup/) 已经介绍了如何定时备份 Ghost 博客数据到本地的一个.tar.gz文件。

但严格来说，这种备份不是很有效，毕竟在同一台服务器。因此，最好的办法是寻找==一个异地备份==。

对于个人博客来说，单独搭建一个异地服务器的成本太高，倒不如利用现有的云存储实现，更为方便和节约。而 Dropbox 恰好可以实现。下面将具体介绍方法。

### 1. 创建 Dropbox App

Dropbox 除了提供客户端程序外，还开放了 API 供开发者使用。登录 Dropbox 后在左下角会有“开发人员”的选项，点击进入：
![](http://ok4jsyu7n.bkt.clouddn.comhttps://cdn.imshuai.com/images/2017/01/dropbox_developer-1.jpg)

依次点击My Apps → Create App → Dropbox API → App folder

![](http://ok4jsyu7n.bkt.clouddn.comhttps://cdn.imshuai.com/images/2017/01/Dropbox-guid.png)

（为了安全，**建议第二步选择App folder**，将操作限制在一个单独的文件夹内）

在生成的 App 里，点击 Generated access token，生成一串 token 字符串，供下面使用。

###  2. 测试 Dropbox 上传脚本

到服务器后台，下载 Github 上已经封装好的shell脚本，更方便的直接调用 Dropbox API：
```
wget https://raw.github.com/andreafabrizi/Dropbox-Uploader/master/dropbox_uploader.sh
```

第一次直接运行此脚本，会提示输入 token，输入之前 Generate 的 token 字符串即可。

在服务器上创建一个 test.txt 文件，使用 dropbox_uploader.sh 上传，命令如下：
```
dropbox_uploader.sh test.txt test.txt
```
意思是将本地的 test.txt 文件上传到服务器上，路径和文件名也是 test.txt。成功后，在 Dropbox 里可以看到成功上传的文件。

### 3. 自动上传脚本

之前，我们已经自动在 /root/ghost/backup 生成了 .tar.gz 的备份文件，所以只用执行如下命令，就完成了备份到 Dropbox ：
```
dropbox_uploader.sh /root/ghost/backup/xxx.tar.gz xxx.tar.gz
```

结合之前凌晨3点导出备份文件的脚本，使用下面的脚本进一步上传到 Dropbox ：
```
#!/bin/bash
# Dropbox 上传Ghost备份文件 by imshuai.com

# GHOST备份文件所在的目录，在这个目录里面，每天生成一个ghost*.tar.gz文件
GHOST_BACKUP_DIR=/root/ghost/backup
# dropbox 上传脚本
DROPBOX_UPLOAD_SHELL=/root/dropbox_backup/dropbox_uploader.sh

cd $GHOST_BACKUP_DIR
# 获取备份目录下的最新一个备份tar.gz文件
backupFileName=`ls -1tr ghost*.tar.gz |tail -n 1`

if [ x"$backupFileName" = x"" ];then
	echo "can not find backup file in folder $GHOST_BACKUP_DIR"
	exit 1
fi

# 将最新的备份上传
$DROPBOX_UPLOAD_SHELL upload $GHOST_BACKUP_DIR/$backupFileName $backupFileName

```

在原来的 crontab 里增加一条，凌晨 3:15 将 backup_ghost.sh 产生的.tar.gz 文件上传Dropbox ：
```
0 3 * * * /root/ghost/backup/backup_ghost.sh
15 3 * * * /root/dropbox_backup/upload_ghost_backup.sh
```

### 4. 总结
1. Dropbox API 提供了不用客户端完成Dropbox文件同步的接口。
1. 借用 Github 上的开源 shell 脚本调用 Dropbox API 更方便。
1. crontab 后台自动调用，实现每日自动上传备份。
1. 该方法也适用于其他服务器环境备份到Dropbox。
