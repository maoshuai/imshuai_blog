---
title: 吐槽：macOS拙劣的窗口管理
date: '2017-06-16 11:55:49'
tags:
- macos
---

虽然macOS在不少方面比Windows设计的更优秀，尤其是Unix血统+软硬件完美的结合，对设计师和码农来说，堪称完美，但——


<!--more-->


macOS在窗口管理上的反人类设定，至今仍让我无法释怀，不得不吐槽一下。

### Dock
Dock作为程序启动器有几点不足：

1. Dock本身独占一行屏幕，==两边的空间白白浪费掉了==。唯一的作用就是让你永远看得到桌面壁纸是什么。
2. Dock默认的放大镜效果华而不实，跳动的图标使得操作更具有不确定性。（现在的版本，已默认关闭放大效果，算是迷途知返了）
2. Dock仅可以显示应用的一个图标，而不是以窗口为单位；无法实现完整的“切换窗口”的功能。
3. macOS==显示了所有虚拟桌面的图标==，使得虚拟桌面的分离性降低。当你点击的图标在另外一个桌面的时候，居然要==点两下==才会自动切换过去！

### Exposé
Exposé可以说最为华而不实，四散开来的窗口缩略图，看起来很酷，但操作效率极低：

1. 通过Exposé切换窗口需要2步操作，先要Exposé展现所有窗口的缩略图；才能从缩略图中选择要切换的窗口。而对比Windows的任务栏“一步到位”的点击操作，实在无法比拟。
2. Exposé后，窗口的缩略图排布具有==不可预测性==，你必须在一堆缩略图中，慧眼识珠。并且缩略图的排列具有==不稳定性==，即新的窗口加入或消除，都会导致Exposé后的窗口缩略图全部重排。这样的设定，导致Exposé在管理5个窗口的时候还勉强可用，再多窗口时，从一堆凌乱的缩略图找到目标窗口太考验眼力了！

Dock和Exposé的不给力，直接导致在macOS上切换窗口非常低效而费力。Windows一个朴素的任务栏完全就解决的问题，被macOS做的如此凌乱。这也催生了像[Ubar](https://brawersoftware.com/products/ubar)这样模拟Windows任务栏的软件存在。

### ⌘-Tab
macOS和Windows都可以通过tab键，快速切换窗口，初看没什么区别；但实际使用起来，macOS体验要差一大截。主要问题如下：

1. ⌘-Tab列出的切换内容，不仅包含窗口，==还包括所有没有窗口的活动应用==。也就说是一个程序已经没有窗口了（比如Preview程序）也会出。并且，这时候你如果切换到这个图标，似乎没什么反应，除了系统菜单栏变成了目标程序外。
2. 多桌面的情况下，列出的总是所有桌面的程序。但你用⌘-Tab切换，并不会自动滑动桌面。
3. 最小化的应用，⌘-Tab切换后，并不会自动将最小化的窗口还原。

综上所述，==macOS的⌘-Tab列出了一大堆、实际上根本无法切换的图标==。

还好，这一点可以通过安装第三方的管理软件[Witch](https://manytricks.com/witch/)解决。

### 全局菜单栏
全局菜单栏的不足在于：

1. ==永远只能显示一个程序的菜单栏==。即便你并排操作两个程序，也只能看到一个窗口的菜单栏。要操作未激活程序的菜单栏，需要走两步：先激活目标程序的窗口，然后在点击全局菜单栏。
2. ==不可去除==。对于某些完全不需要菜单栏的程序（比如有道词典），或者个性化操作界面的程序（比如Office的Ribbon页签），简直就是拖油瓶。这个时候，菜单栏的唯一作用就是显示应用名称ㄟ( ▔, ▔ )ㄏ

用一句话总结全局菜单栏：你要或不要，==有且仅有一个==全局菜单栏呆在那里。
