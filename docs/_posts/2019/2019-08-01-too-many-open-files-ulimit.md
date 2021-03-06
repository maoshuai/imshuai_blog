---
class: post-template
navigation: True
title: 第3期：Too many open files以及ulimit的探讨
date: '2019-8-01 18:25:23'
tags:
- Java与Linux学习周刊
- Java
- Linux
---

`Too many open files`是Java常见的异常，通常是由于系统配置或程序打开过多文件导致。这个问题常常又与`ulimit`的使用相关。关于`ulimit`的用法有不少坑，本文将遇到的坑予以梳理。


<!--more-->

# Too many open files异常
下面是Java在系统超过最大打开文件数时的异常堆栈：
```java
Exception in thread "main" java.io.IOException: Too many open files
	at java.io.UnixFileSystem.createFileExclusively(Native Method)
	at java.io.File.createTempFile(File.java:2024)
	at java.io.File.createTempFile(File.java:2070)
	at com.imshuai.wiki.ulimit.App.main(App.java:16)
```
如果不是程序问题（程序问题，需要看为什么打开很多文件，比如通过lsof），一般要通过ulimit调整打开文件数限制解决，但ulimit本身也有不少坑，下面做一下总结。

# 什么是ulimit
直接参考ulimit的帮助文档（注意：不是man ulimit，而是help ulimit，ulimit是内置命令，前者提供的是C语言的ulimit帮助）：
>  Modify shell resource limits.
>  
>  Provides control over the resources available to the **shell and processes it creates**, on systems that allow such control.

可以看出，**ulimit提供了对shell（或shell创建的进程）可用资源的管理**。除了打开文件数之外，可管理的资源有：
最大写入文件大小、最大堆栈大小、core dump文件大小、cpu时间限制、最大虚拟内存大小等等，help ulimit会列出每个option限制的资源。或者查看`ulimit -a`也可以看出：
```
maoshuai@ms:~/ulimit_test$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) 100
pending signals                 (-i) 15520
max locked memory       (kbytes, -l) 16384
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 15520
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

# 理解ulimit
在使用ulimit之前，有几个容易迷糊的点：
## ulimit的管理的维度
理解ulimit，第一个疑问是限制的维度是什么。比如nofile设置为1024，是指**当前用户**总共只能打开1024个文件，还是**单个shell会话进程**只能打开1024个文件？**  实际上help ulimit里已经说清楚了：process，但我们可通过下面的方法程序验证：

下面通过一段java程序，打开800个文件：
```java
class Ulimit{
    public static void main( String[] args ) throws IOException, InterruptedException
    {
    	List<FileInputStream> fileList = new ArrayList<FileInputStream>();
    	for(int i=0;i<800;i++) {
    		File temp = File.createTempFile("ulimit-test", ".txt");
    		fileList.add(new FileInputStream(temp));
    		System.out.println("file_seq=" + i + " " + temp.getAbsolutePath());  
    	}
    	// keep it running, so we can inspect it.
    	Thread.sleep(Integer.MAX_VALUE);
    }
}
```

我们将nofile设置为1024
```shell
ulimit -n 1024
```
然后我们运行两个进程实例：
```shell
nohup java Ulimit >a.log &
nohup java Ulimit >b.log &
```
查看日志a.log和b.log，都创建了800个文件，没有报异常。

如果将ulimit 设置为700，重新测试，发现java程序在创建688个文件时就报了`Too many open files`异常(之所以不是700整，是因为java本身也会打开一些文件)，
```java
file_seq=688 /tmp/ulimit-test7270662509459661456.txt
Exception in thread "main" java.io.IOException: Too many open files
        at java.io.UnixFileSystem.createFileExclusively(Native Method)
        at java.io.File.createTempFile(File.java:2024)
        at java.io.File.createTempFile(File.java:2070)
        at Ulimit.main(Ulimit.java:12)
```
虽然ulimit的u是user的意思，但事实证明，**ulimit控制的维度是shell会话或shell创建的进程（至少对于nofile来说）。即：当前用户打开的文件数，是可以远远超过nofile的值。** 

所以，通过`lsof | wc -l` 查看系统打开文件数，来判断是否打开文件数是否超了，是不正确的。另外，`lsof | wc -l` 是也并不反映系统打开的文件数！（后续周刊补充）

## soft和hard的区分
理解ulimit第二个重要方面是soft和hard的区分，ulimit对资源的限制区分为soft和hard两类，即同一个资源（如nofile）存在soft和hard两个值。

在命令上，ulimit通过-S和-H来区分soft和hard。如果没有指定-S或-H，在显示值时指的是soft，而在设置的时候指的是**同时设置soft和hard值**。

但soft和hard的区别是什么是什么呢？下面这段解释较为准确（来自man 2 getrlimit ）

> The soft limit is the value that the kernel enforces for the corresponding resource.  The hard limit acts as a  **ceiling  for  the soft  limit**:  an unprivileged process may set only its soft limit to a value in the range from 0 up to the hard limit, and** (irre‐versibly) **lower its hard limit.  A privileged process (under Linux: one with the CAP_SYS_RESOURCE capability) may make  arbitrary changes to either limit value.

归纳soft和hard的区别：
1. 无论何时，soft总是小于等于hard
2. 无论是超过了soft还是hard，操作都会被拒绝。结合第一点，这句话等价于：超过了soft限制，操作会被拒绝。
2. 一个process可以修改当前process的soft或hard。但修改需满足规则：
  * 修改后soft不能超过hard。也就是说soft增大时，不能超过hard；hard降低到比当前soft还小，那么soft也会随之降低。
  * 非root或root进程都可以将soft可以在[0-hard]的范围内任意增加或降低。
  * 非root进程可以降低hard，但不能增加hard。即nofile原来是1000，修改为了900，在修改为1000是不可能的。（这是一个单向的，有去无回的操作）
  * root进程可以任意修改hard值。

soft和hard在控制上其实并没有区别，都会限制资源的使用，但soft可以**被进程在使用前自己修改**。

## ulimit的修改与生效
知道ulimit很好，但更重要的是怎么修改，这是工作中常见的任务。

关于ulimit的生效，抓住几点即可：
1. ulimit的值总是继承父进程的设置。
2. ulimit命令可修改当前shell进程的设置。这也说明，**为了保证下次生效，修改的地方要具有持久性**（至少相当于目标进程而言），比如.bashrc，或进程的启动脚本）
3. 从第2点也可以推出，运行中的进程，不受ulimit的修改影响。
3. 增加hard值，只能通过root完成

下面给出两个案例：

### 案例1：某非root进程要求2048的nofile，经查看当前soft为1024，hard为4096
可以直接在该进程启动脚本中，增加ulimit -nS 2048即可

### 案例2：某非root进程要求10240的nofile，经查看当前soft为1024，hard为4096
显然，非root用户没法突破。只能通过root修改，一般修改`/etc/security/limits.conf`文件，修改方法在该配置文件中的注释中也有说明，格式是：

一条记录包含4️列，分别是范围domain（即生效的范围，可以是用户名、group名或*代表所有非root用户）；t类型type：即soft、hard，或者-代表同时设置soft和hard；项目item，即ulimit中的资源控制项目，名字枚举可以参考文件中的注释；最后就是value。比如将所有非root用户的nofile设置为100000
```shell
*  hard nofile 10000
*  soft nofile 10000
```

## 运行中进程的limits的查看
ulimit修改之后，可以直接通过ulimit命令查看。**对于已运行的进程，还有一种更准确的查看方法**（比如修改ulimit前就启动的进程，如何知道其ulimit值就需要这种方法）：查看进程目录下的limits文件。比如，/proc/4660/limits文件就记录了4660号进程的所有limits值：

```shell
maoshuai@ms:~/ulimit_test$ cat /proc/4660/limits 
Limit                     Soft Limit           Hard Limit           Units     
Max cpu time              unlimited            unlimited            seconds   
Max file size             unlimited            unlimited            bytes     
Max data size             unlimited            unlimited            bytes     
Max stack size            8388608              unlimited            bytes     
Max core file size        0                    unlimited            bytes     
Max resident set          unlimited            unlimited            bytes     
Max processes             15520                15520                processes 
Max open files            2000                 2000                 files     
Max locked memory         16777216             16777216             bytes     
Max address space         unlimited            unlimited            bytes     
Max file locks            unlimited            unlimited            locks     
Max pending signals       15520                15520                signals   
Max msgqueue size         819200               819200               bytes     
Max nice priority         0                    0                    
Max realtime priority     0                    0                    
Max realtime timeout      unlimited            unlimited            us 
```

## ulimit不加参数
曾经有小白直接用`ulimit`查看，看到打印出`unlimited`，就认为打开文件不受限制。显然这是不对的，`help ulimit`中明确指出：
> If  no option is given, then -f is assumed.

所以，ulimit不加参数，相当于`ulimit -f -S`（没有指定-S或-H就相当于-S），实际上是指可写入的文件最大size。

# 其他

## 如何查看系统打开文件数
losf命令虽然作用是"list open files"，但用`lsof | wc -l`统计打开文件数上非常不准确。主要原因是：

* 某些情况下，一行可能显示的是线程，而不是进程，对于多线程的情况，就会误以为一个文件被重复打开了很多次
* 子进程会共享file handler
如果用`lsof`统计，必须使用精巧的过滤条件。更简单和准确的方法是，通过/proc目录查看。获取系统打开文件说，直接查看/proc/sys/file-nr，其中第一个数字就是打开的file数（file-nr说明参考：[https://www.kernel.org/doc/Documentation/sysctl/fs.txt](https://www.kernel.org/doc/Documentation/sysctl/fs.txt)）。要查看一个进程的打开文件数，直接查看目录/proc/$pid/fd里的文件数即可：

## Java 自动将nofile的soft提升为hard上限
在研究的过程中，**我发现java程序似乎不受nofile的soft值影响**。查看进程的limits文件（/proc/$pid/limits），才发现nofile的soft被提升为和hard一样。经过全网搜索查询，**发现JDK的实现中，会直接将nofile的soft先改成了和hard一样的值**，可参考：[How and when and where jvm change the max open files value of Linux?
](https://stackoverflow.com/questions/30487284/how-and-when-and-where-jvm-change-the-max-open-files-value-of-linux)

## Ubuntu中eclipse中启动的java和命令行启动的java，nofile不一样
通过pstree，发现eclipse的java是通过gnome-shell启动的，而命令行是通过gnome-terminal启动的。其中gnome-terminal又是通过systemd --user启动的，而systemd --user似乎不读取/etc/security/limits.conf的值。这个坑的说明有机会再填吧。

## file-max控制内核总共可以打开的文件数
除了ulimit控制外，`/proc/sys/fs/file-max`这个文件控制了系统内核可以打开的全部文件总数。所以，即便是ulimit里nofile设置为ulimited，也还是受限的。

## ulimit常用选项
```shell
ulimit -a # 查看所有soft值
ulimit -Ha # 查看所有hard值
ulimit -Hn # 查看nofile的hard值
ulimit -Sn 1000 # 将nofile的soft值设置为1000
ulimit -n 1000 # 同时将nofiles的hard和soft值设置为1000
```

# 参考
* [Session failures with error "Too many open files"](https://community.pivotal.io/s/article/Session-failures-with-Too-many-open-files)
* [ulimit man page](https://ss64.com/bash/ulimit.html)
* [`lsof | wc -l` sums up a lot of duplicated entries](https://unix.stackexchange.com/questions/36841/why-is-number-of-open-files-limited-in-linux)
* [How and when and where jvm change the max open files value of Linux?](https://stackoverflow.com/questions/30487284/how-and-when-and-where-jvm-change-the-max-open-files-value-of-linux)
* [Why file-nr and lsof count on open files differs? ](https://unix.stackexchange.com/questions/176967/why-file-nr-and-lsof-count-on-open-files-differs)

> 本文转自我的知乎专栏[《Java与Linux学习周刊》](https://zhuanlan.zhihu.com/java-linux)的[《第3期：Too many open files以及ulimit的探讨》](https://zhuanlan.zhihu.com/p/75897823)。
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTYwNDU3MDA4LDM2NDc5NDU2Ml19
-->
