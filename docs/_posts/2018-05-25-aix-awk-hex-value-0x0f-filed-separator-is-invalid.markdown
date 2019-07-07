---
layout: postclass: post-templatenavigation: True
title: AIX下awk使用16进制分隔0x0f符无效
date: '2018-05-25 07:33:31'
---

今天同事用db2用0x0f导出的del文件，想进一步用awk分析，不知道如何写awk分隔符；我想应该就是-F参数转义一下而已，但发现直接写`awk -F '\0x0f'`居然没有作用。
查询了一下发现原来是`AIX`的awk是不支持16进制表示的ascii码，只支持8进制，所以改成`awk -F '\17'`即可。
