---
class: post-template
navigation: True
title: 变量命名的各种case
date: '2021-04-07 22:25:23'
tags:
- computer-knowledge
---

[Phil Karlton的儿子曾证明他父亲说过](https://skeptics.stackexchange.com/questions/19836/has-phil-karlton-ever-said-there-are-only-two-hard-things-in-computer-science)：There are only two hard things in Computer Science: cache invalidation and naming things.

命名是那么的重要，而命名的一个重要问题是怎么分割单词。

<!--more-->

自然语言中，空格是分割单词的符号，而在程序语言变量的命名上，空格使用空格会产生不必要的麻烦。另外，即便是在某一个变成语言中，通常也习惯采用多种命名风格来区分不同的代码元素（如变量、方法、常量等）。

程序员写程序的第一课，也许就是学习变量的命名吧。

最著名的命名方式大概是驼峰式（Camel Case）和帕斯卡式（Pascal Case）。也代表了命名风格上的两个方向：
* 使用非字母数字的分隔符，比如下划线（Underscore）或中划线（Hyphen）分割单词。
* 使用大小写转换分割单词

当然，也可以混合上述两种方式，下面是[维基百科](https://en.wikipedia.org/wiki/Naming_convention_(programming))整理的几种格式和对应的英文名称：

**Formatting**|**Name(s)**
:-----:|:-----:
twowords|flat case
TWOWORDS|upper flat case
twoWords|(lower) camelCase, dromedaryCase
TwoWords|PascalCase,Upper Camel Case,StudlyCase
two\_words|snake\_case, pothole\_case
TWO\_WORDS|SCREAMING\_SNAKE\_CASE, MACRO\_CASE, CONSTANT\_CASE
two\_Words|camel\_Snake\_Case
Two\_Words|Pascal\_Snake\_Case
two-words|kebab-case, dash-case, lisp-case
two\|words|doner\|case
TWO-WORDS|TRAIN-CASE, COBOL-CASE, SCREAMING-KEBAB-CASE
Two-Words|Train-Case, HTTP-Header-Case

