---
layout: post
title: 诡异的现象：df报文件系统满，但du看差别很大
date: '2017-08-08 08:25:44'
tags:
- linux
---

今天在处理一个问题，最后发现是文件系统满（file system full）造成的，`df`看一下，果然`/opt`文件系统的Use%变成了100%。于是马上到`/opt`目录下用du命令看一下是哪个地方出现了大文件把文件系统撑爆了：`du -sm | sort -nr`

`/opt`目录分配了30G的空间，我看了一下前几名的占用也不过10G左右。进一步，直接执行`du -sh /opt` 发现`/opt`目录的文件总和也不过12G，这就奇怪了，莫非18G被系统吃了？？？

无奈Google了一下`du df different results`，发现原来是==系统存在删除的文件没有释放==导致。

具体来说是，某进程持有对某文件的引用，但此文件直接被删除了，该进程实际上还是能写入该文件，只不过该文件已经在文件系统内不可见。这才导致du的结果和df不一致！

具体验证方法，就是`lsof |grep '(deleted)'`，果然看到了我之前删除的一个大日志文件。赶紧重启了该进程，一切都正常了。

### 为什么删除的文件可以继续使用？
Linux下文件系统使用inode进行管理。对进程来说，对文件的引用并不是通过路径名实现，所有的路径名都要转换成一个inode的结点id，路径名只不过是提供给“用户”的概念。
正式基于此，Linux下竟然可以在进程使用的时候，对占用的文件进行重命名甚至删除操作。因为，进程根本不关心路径名！