---
title: 直男程序员的Windows使用偏好
date: '2018-05-23 13:23:20'
tags:
- summary
- tools
- time
---

以下是我关于Windows使用的一些偏好设置，总的来说是**效率优先**，可参考：

# 显示所有文件拓展名
这是程序员的基本素质，我的原则是，做不到者**直接断交**！竟然多次有同事问我：我已经把拓展名改成了.sh，为什么还是不行？那是你以为的！

![show-extended-name-1](https://cdn.imshuai.com/images/2018/05/show-extended-name-1.png)

# 任务栏放在上方
理由：Windows应用的菜单栏都是在窗口的上方，内容也是从上到下看，大部分时间**视线都是集中在屏幕上半部分**。而默认放在下方的任务栏，**会导致眼球频繁上下移动**，易分散了注意力和降低操作效率。

![windows-layout](https://cdn.imshuai.com/images/2018/05/windows-layout.png)

# 任务栏启用XP风格
即长方形任务栏，不合并相似任务。
Win7 以后，任务栏默认显示为一个正方形图标（还是**很大**的正方形，我看到任务栏就占了1/5空间的屏幕），并且合并相同程序的任务。尤其是合并相似任务，导致原本一步到位的切换操作，要拆分成两步，效率底下。做以下设置：

![taskbar-preference](https://cdn.imshuai.com/images/2018/05/taskbar-preference.png)

# 取消任务栏Aero Peak
我实在想不出 Aero Peak 的用途，除了让你某个瞬间，鼠标不小心滑动到边缘，突然透明的看到桌面！

# 取消拖沓的动画
尤其是窗口的最大化和最小化，相当耗时，禁用后显得简洁快速多了。

# 文件属性的排列参考 Unix 命令
**核心的原则是：将长度固定的列放在前面（比如修改日期），长度变化大的列放在最后（比如文件名）**，这样可以有效减少文件名显示不全，经常手动调整列宽的问题。

![windows-explorer-layout](https://cdn.imshuai.com/images/2018/05/windows-explorer-layout.png)

这个原则是参考Unix的 ls -l 命令排布：

![unix-ls-layout](https://cdn.imshuai.com/images/2018/05/unix-ls-layout.png)

# 将壁纸设置为纯黑色
个人喜好，减少干扰。

# 保持桌面的整洁
没事不要把文件存在桌面上，显得太乱。

# 将数据分区盘符固定修改为G:
如果有单独的数据分区的话，将分区固定的设置为G：,基本可以避免盘符冲突，保证**所有电脑的盘符一致！文件夹树形结构一致**。

# 不要将文档放到默认的 Documents 目录
OS 的初衷是好的，帮你建立很个文件夹（文档、图片、音乐），但相信我，不要去用这些目录：因为这些目录会被很多应用程序写成一个垃圾场。

# 取消删除文件的确认对话框
取消删除文件到回收站的对话框，采用**静默删除到回收站，定期清理回收站**的策略，省时又安全。如下图，不要勾选“显示删除确认对话框”。
![recycle-confirmation](https://cdn.imshuai.com/images/2018/05/recycle-confirmation.png)

理由是：
1. 误操作后，从回收站可恢复，这个提醒没有任何必要，浪费时间：
![recycle-remove-confirm](https://cdn.imshuai.com/images/2018/05/recycle-remove-confirm.png)

2. shift+del 极不推荐，手熟了会形成条件反射，勿删后悔都来不及。并且避免不了二次确认，浪费时间！

# 取消无用的开机启动
比如迅雷。
只有老年人和萌妹子的电脑会有很多启动项！

# 使用休眠代替关机
从 Windows 7 开始，休眠已非常安全。我在公司使用的Windows每天都是休眠；第二天来公司，相比其他人开机更快，并且上次的工作现场得以保留。单这一点，可以让你**每天领先同侪至少30分钟**！
顺便，将开始菜单的关机键替换为休眠键：
![make-poweroff-hibernate](https://cdn.imshuai.com/images/2018/05/make-poweroff-hibernate.png)


# 取消任务栏上无用的图标
系统默认的什么Media Player之流，百年也用不到，放着干嘛？安装第三方的软件不要没事就往任务栏和桌面放图标。要知道什么是寸土寸金！

# 任务栏时间显示到秒
一个时间精度要求高的人，手机、电脑都必须是秒级准确性！

# 开启全文搜索
Windows 7之后的全文搜索应经很优秀了，建议开启。寻找各种文档，没有死角！

# 使用SSD固态硬盘
每个同事都问我，为什么我的电脑4G内存比他16G的感觉还快，我只能告诉他我换了SSD。
