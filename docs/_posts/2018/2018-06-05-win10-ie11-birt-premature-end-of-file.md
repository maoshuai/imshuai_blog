---
title: 'WIN10 IE11 下BIRT报表报org.xml.sax.SAXParseException: Premature end of file.'
date: '2018-06-05 07:50:20'
tags:
- summary
---

今天项目组反馈，BIRT做的报表到WIN10环境兼容性测试时有个奇怪的问题，查了一个星期还没解决。现象如下：

<!--more-->


在WIN7下用IE8无论连接UAT服务器WAS还是本地JBOSS都没有问题。在WIN10的IE11兼容模式下，连接UAT服务器就报“org.xml.sax.SAXParseException: Premature end of file”，奇怪的是连接JBOSS就不报错。

同时刚对我描述，我初步判断应该是浏览器兼容性问题导致，但又说在JBOSS下就没问题，那说明应该是后台的问题。

首先查看了一下报错，只是弹窗：

![PrematureEOF](https://cdn.imshuai.com/images/2018/06/PrematureEOF.jpg)

首先打开浏览器开发者模式，查看发现网络请求frameset返回500 Internal Server Error，因此问题转移到后台，通过比对同一个操作的正常和异常的日志，发现异常情况下后台无法正常解析，会把整个SOAP报文作为一个字段解析。因此怀疑前台传递的请求报文有问题，对比报文格式，没有发现任何问题。

进一步查看http header，也没有发现问题。其中注意到，content-type都是application/x-www-form-urlencoded, text/xml; charset=UTF-8，隐约的觉得可能有问题，但再次查看，发现WIN10下无论是请求JBOSS还是WAS都是一样的content-type，因此就略过了。

最后，找不到原因，Google吧，搜索“birt server.userexception premature end of file”，果然有人遇到类似问题：https://www.eclipse.org/forums/index.php/t/198414/ ，进一步跟跟进里面的回答，找到这是BIRT的bug：https://bugs.eclipse.org/bugs/show_bug.cgi?id=310512 ，

>Just to summarize, when using chrome/Tomcat to view our BIRT content I do not get any errors, I only get the "premature end of file error" when using chrome against our app deployed on our supported application  servers (websphere or weblogic). 

至此，这个问题解决了，按照bug上的解决方法：
将webcontent/birt/ajax/lib/prototype.js 删除668和669行。




## 经验总结：
1. 如果用正常、异常的样本，比对两者差异，可以快速找到问题点。
2. 杀手锏：Google+英文，搜索错误关键字。95%的问题，你都不是第一个遇到的。
3. 问题的表面现象和本质原因有一定的距离。就像这个问题开始以为是“前台问题”，然后怀疑是后台问题，再怀疑是前台，最终发现是前台+后台的综合问题。
4. BUG总是出其不意，升级后的测试永远是必要的。
