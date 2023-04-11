---
class: post-template
navigation: True
title: 有道云笔记批量迁移到wiz笔记方案——逃离有道云笔记
date: '2021-03-29 22:25:23'
tags:
- writing
---



当初是脑子进了什么水，才会使用有道云笔记？要迁移时才知道真的是没节操，根本不给你导出到其他笔记软件的路径。数据进入有道云笔记就是一条死胡同。

![](https://cdn.imshuai.com/images/2021/03/conerting-of-notes.png)

<!--more-->

记得刚使用有道云笔记时，还有导出evernote格式的选项，至少给了一条出路。现在只能批量导出私有格式或者PDF格式。



本次准备转到wiz笔记（至少wiz笔记的存储格式就是html压缩成的ziw文件，目前看来还是比较放心的），测试下来html格式导入wiz的效果最好。



通过一番探索，终于找到曲线救国的路径：

有道Open API导出XML格式->转换成markdown格式->转换成html格式。测试下来效果可以接受。



具体步骤如下：

1. 使用有道云笔记的Open API导出成xml数据文件并转换为markdown格式，这个工作有人做了，参考https://github.com/DeppWang/youdaonote-pull

2. 通过[pandoc](https://pandoc.org/)批量将markdown文件转换为html文档（markdown文件已经导入到youdaonote目录）

   ```shell
    find youdaonote -name "*.md" | while read file;do pandoc -f markdown -t html5 "$file">"${file%.*}.html";done
   ```

   

3. 通过wiz批量导入html文件夹


