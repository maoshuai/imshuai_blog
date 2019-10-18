---
title: Linux查看和修改时区
featured: true
date: '2017-01-17 14:23:47'
tags:
- ghost-blog
- linux
---

对于部署在海外的Linux服务器来说，拿到的机器时区和本地并不一样，导致运行在上面的应用也面临时区问题。所以有必要修改系统本地时区。

### 1. 查看时区
查看当前生效的时区，可以简单的通过`date`命令查看当前时间：
```
[root@shuai01 zoneinfo]# date -R
Tue, 17 Jan 2017 21:36:23 +0800
```
最后的+0800，即东8区。

### 2. 设置时区
1. 然并卵的`tzselect`命令
    看起来很像一个时区选择的工具，但并非如此。事实上`tzselect`仅仅是一个==查看时区表示方式的『向导』程序而已==。通过依次询问大洲→国家→城市，最后告诉你如何TZ变量的写法，比如北京时间是：Asia/Shanghai

1. `TZ`变量
   可以通过修改TZ变量，直接修改时区信息，比如：

        [root@shuai01 ~]# date -R
        Tue, 17 Jan 2017 13:57:06 +0000
        [root@shuai01 ~]# export  TZ='Asia/Urumqi'
        [root@shuai01 ~]# date -R
        Tue, 17 Jan 2017 19:57:18 +0600

    有Linux经验的小伙伴都知道，不写在文件里的设置更改很难生效一般是会话级的，重新登录会消失。所以，这样直接修改TZ的尿性绝对做不到持久化更改时区的。

    正确的方式是到`/etc/profile`里（或用户的.profile或.bashrc文件），直接export `TZ`变量为要更改的时区（时区的名字可以用`tzselect`向导来确定）

1. `/etc/localtime`文件
   默认情况下情况下，TZ属性是空，这时候是靠`/etc/localtime`文件来确定的时区。而此文件通常又是一个到`/usr/share/zoneinfo/`下各种时区文件的软连接。通过修改`/etc/localtime`指向的软连接，进而修改系统的时区。比如下面的方法，将localtime文件设置为了北京时间：

        ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

### 3. 总结
1. `tzselect`命令无法修改时区，仅给出时区的城市表示法
1. `TZ`变量和`/etc/localtime`文件会影响时区，并建议直接修改`/etc/localtime`文件。
1. 如果在shell中临时需要变更时区信息，可以修改`TZ`变量实现。
1. 在`profile`文件里设置变量`TZ`，达到和修改`/etc/localtime`类似的效果。

---
### 更新
1. 2017-01-19
设置完时区，最好做一次服务器重启（或相关service的重启）。否则像crontab这样的服务，会仍然沿用原来的时区。
