---
layout: postclass: post-templatenavigation: True
title: Sonatype Nexus maven私服group仓库构件下载缓慢问题
date: '2018-08-07 02:22:09'
tags:
- summary
---

部门内供大家使用的maven私服，下载构件的速度越来越慢了，很多同事频繁反映打个包光下载构件就要一个小时，苦不堪言。

私服上是建立了一个group仓库，聚合了包括maven官方中央仓库、私服的release、snapshot、thirdparty等仓库。然后将group仓库的地址提供给所有开发人员使用。**并且这台maven私服是无法访问外网的**。

我看了一下，有几个现象：
1. 每天第一次下载某个构件，速度会非常缓慢，经常卡在downloading的过程，单个构件的下载估计在1分钟左右。
2. 一旦某个构件下载完毕后，即便是将本地缓存删除，再次下载就也非常迅速。
3. 通过group下载本地某个在私服release、thirdparty上的某个构件会非常慢，但改为直接用release或thirdparty的地址下载却非常快。

根据上述现象，尤其是第三点，初步怀疑是group聚合多个仓库出现了问题。网上搜了一下，对某个构件，maven会先将group聚合的所有的具体仓库做merge，再决定用哪个仓库的构件（因为多个仓库可能存在坐标一样的构件）。为了做merge，**就要将所有仓库的信息都去查一下，maven的做法是隔一段时间（默认是1天）检查一次**。

如果是这样，在检查中央仓库的时候岂不是杯具了？根据指导，在Nexus中增加了"remote.storage.outbound"的debug级别日志，检查了一下私服的所有对外流量，**发现每个私有构件都要先到中央仓库下载，并且是20s后超时，重试3次放弃**。原本基本确定了。

修改方法：
到Central仓库的Configuration中，修改HTTP Request Settings，修改Request Timeout为1s，并将Request Retry Attempts修改为0。这样可以大大降低重试的时间。

参考资料：
[Troubleshooting slow maven-metadata.xml download speeds](https://support.sonatype.com/hc/en-us/articles/213465188-Troubleshooting-slow-maven-metadata-xml-download-speeds)
