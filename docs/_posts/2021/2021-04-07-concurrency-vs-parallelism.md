---
class: post-template
navigation: True
title: 并发vs并行
date: '2021-04-07 22:25:23'
tags:
- 咬文嚼字
---

日常开发中，我们会说：“XX代码是并行执行的，要注意线程安全问题”，“通过多线程并发执行，提高了系统性能”，但实际上并行和并发还是有差异的。

<!--more-->

# 异同分析

实践中，并发（Concucrrency）是比并行（parallelism）两个概念通常可以混淆，可以指“同时执行多个任务”。但两个概念有本质差异，

首先分析两个概念的要素：
* “多个任务”是前提，如果只有一个任务，则不存在并行或并发。
* 同时执行是特点，但“同时”可能表象上的同时，也可能是真正的同时。

简单的说，**并发可以指系统通过快速切换任务，使得多任务交错执行，从而提供感觉上的“同时”执行；而并行，是指在某一个时刻，真实的有多个任务在同时执行**。广义上，**并发是比并行更宽泛的概念，即并发包含并行**。

如果拿线程在CPU上执行为例说明，**并行只能在多核CPU上执行**，而并发可以在单核CPU上，通过操作系统的任务调度实现时间片轮转执行。

更常见的情况是，并行和并发是混合出现的，而操作系统或编程框架透明的帮我们处理了这些。比如Java中提供的是concurrent包，而不是parallelism，这个用词是严谨的；我们开发的多线程应用，可能是并发的执行，也可能是并行的执行，更可能是既并发又并行的执行。


# 参考定义
下面罗列了几种定义，从不同的角度进行了阐述，供参考：


* [Stackoverflow]

    Concurrency is when two or more tasks can start, run, and complete in overlapping time **periods**. It doesn't necessarily mean they'll ever both be running at the same instant. For example, multitasking on a single-core machine.

    Parallelism is when tasks literally run **at the same time**, e.g., on a multicore processor.

    这个定义，侧重于时间段和时刻。

* [Oracle Multithreaded Programming Guide](https://docs.oracle.com/cd/E19455-01/806-5257/6je9h032b/index.html)

    Concurrency: A condition that exists when at least two threads are **making progress**. A more generalized form of parallelism that can include time-slicing as a form of virtual parallelism.

    Parallelism: A condition that arises when at least two threads are **executing** simultaneously. 

    并发是多个线程都在推进，体现的是两个任务都没有拉下；而并行是多个任务在同时执行。

* [Stackoverflow](https://stackoverflow.com/questions/1050222/what-is-the-difference-between-concurrency-and-parallelism)

    Concurrency: Interruptability

    Parallelism: Independentability

    这个定义很简洁，并发是通过中断实现，而并行是互相独立的。

* [Concurrency vs. Parallelism — A brief view ](https://medium.com/@itIsMadhavan/concurrency-vs-parallelism-a-brief-review-b337c8dac350#:~:text=Concurrency%20is%20about%20dealing%20with,at%20the%20same%20time%20instant.)

    Concurrency is about **dealing with lots of things** at once. Parallelism is about **doing lots of things** at once. 

    这个定义很有人性，并发是“应付”很多事，那种切换任务而疲于奔命的感觉出来了；而并行是“在做很多事”，悠然自得。

* [Java Concurrency -- jenkov](http://tutorials.jenkov.com/java-concurrency/concurrency-vs-parallelism.html)
    
    一图胜千言：

    并发

    ![](https://cdn.imshuai.com/images/2021/04/concurrency-vs-parallelism-1.png)

    并行

    ![](https://cdn.imshuai.com/images/2021/04/concurrency-vs-parallelism-2.png)
