---
layout: postclass: post-templatenavigation: True
title: Headless Browser与服务器端HTML转PDF
date: '2017-07-23 13:23:32'
---

最近的项目，要求生成PDF格式的报告书供客户下载或直接发送到Email地址。而且，报告书含有很多图表，基本上是前台HTML页面看到的一样。

这个是一个报表生成的需求，首先从实现的位置看，有两种方案：

1. 由客户端，借助浏览器渲染并生成PDF。
 * 优点：减轻服务器压力，使用HTML转换，与客户端浏览器保持一致。
 * 缺点：无法脱离客户端，遇到需后台发送email的情况，无法实现；不同浏览器无法保证一致性的生成效果。
2. 由服务器端渲染并生成PDF。
 * 优点：可以完全控制生成PDF的效果；可以选择各种语言实现方案
 * 缺点：对服务器压力大；如果用非HTML实现，很难做到和前台一致的效果。

经过比较，结合我需要发送Email的需求，最终选择了服务器端生成。服务器端生成一般有几种方案：

1. 直接操作PDF（比如用Apache的pdfBox），直接被否。原因是：很难实现和前台HTML一致的效果；尤其是制作图表。
2. 使用Word做模板，通过Apache的POI接口操作Word模板，动态生成目标Word，然后再转换为PDF。这种方案，也不是太好：Word如何操作复杂的图表？Word转PDF的效果会失真。
3. 使用HTML转换PDF。如果使用HTML转，可以避免二次开发报表文件的界面，更可以复用前端开发人员。唯一要解决的是：**如何在服务器端渲染HTML**。

经过一番探索，发现现有的方案有：

1. [jsReport](https://jsreport.net/)：商业化软件，提供完善的设计工具、报表服务器（NodeJS实现）以及PDF、XLS等各种转换，甚至提供公有云，直接使用其在线服务。
2. [wkhtml2pdf](https://wkhtmltopdf.org/)：命令行工具，使用Qt WebKit渲染引擎将HTML渲染为HTML
3. [phantomJs的screen capture功能](http://phantomjs.org/screen-capture.html)，phantomJs本身具有完整的Webkit核心，因此渲染出的效果与Chrome兼容性极好。

值得一提的是，jsReport是通过集成各种转换器实现的。而其核心的PDF转换功能，正是通过phantomJS实现。因此，在使用jsReport后，我决定直接使用phantomJS。

主要思路是，JavaEE应用在收到PDF生成请求后，调用服务器上安装好的phantomJS，然后直接请求自身的一个html页面的URL；生成PDF后再提供到客户端，或者直接发送Email。



### headless browser其他用途
headless浏览器，即没有图形界面的浏览器，但又可以实现普通浏览器HTML/JS/CSS的渲染，以及其他基本浏览器功能。

由于它是“无头”的，所以可以部署在服务器端，或者应用在某些自动化工具上，比如：自动化UI测试、对web页面截图（比如本文的PDF生成需求）、爬虫；甚至DDOS攻击、广告刷量等。

根据[维基百科](https://en.wikipedia.org/wiki/Headless_browser)介绍，主流的headless browser有：

1. phantomJS
2. Google Chrome 59以上版本，支持在Linux和macOS上的headless模式
3. HtmlUnit

Github上也有人详细总结了Headless Browers：
https://github.com/dhamaniasad/HeadlessBrowsers
