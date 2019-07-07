---
layout: postclass: post-templatenavigation: True
title: 氢OS3.0开启Google服务
date: '2017-04-12 09:25:51'
---

一加氢OS升级到3.0版本后，基本上和氧OS已融合。底层都是基于Android7.0，并且在UI和操作逻辑上几乎一致，唯一区别仅在搭载的APP上了。

最大的差别自然是，氧OS预装了Google服务和Google全家桶；同时，不含电话号码识别等中国本地化的东西。另外，氧OS也不支持通话录音。

我猜测，在一加内部，两个ROM已经合并，只是在发布的时候，分别添加上本地化服务即可。

另外，据一加用户反馈，只要随便在氢OS3.0内安装Google Play客户端，即自动“激活”了Google服务。这说明，**氢OS本身其实是有Google服务，只不过是被故意屏蔽罢了**。

因此，国内用户没有什么理由去刷氧OS。

下面，是我安装氢OS3.0后，获得Google服务的步骤：

1. 从一加市场上安装Google Play应用。安装完毕，系统设置页就已经出现了Google账号，说明Google服务已激活。
2. 如果要使用Google日历，直接安装Google日历即可。安装后Google日历会出现在Google账号的同步选项了。
3. 如果要使用Google通讯录同步。我们通过重新安装Google通讯录同步软件。但这个软件在Google Play上无法直接找到，可以到[APKMirror](https://www.apkmirror.com/)上搜索下载：Google Contacts Sync并安装。需要注意，要手工将“读取联系人”的权限打开，否则同步选项里依然看不到“联系人”。

当然，也可以通过谷歌安装器或opengapps下载进行安装，但前者一般Google服务的版本比较低，而后者又需要root权限。鉴于氢OS本质上是有Google服务的，使用上面的版本更方便。
