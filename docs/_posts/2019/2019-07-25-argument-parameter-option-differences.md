---
class: post-template
navigation: True
title: 第2期：argument、parameter以及option有什么区别
date: '2019-7-25 20:25:23'
tags:
- Java与Linux学习周刊
- Java
- Linux
---


日常交流中，我们通常用`参数`一词说明函数或者命令的使用方法，比如：
* HashMap 可以通过构造函数的 initialCapacity **参数**设置初始容量，我传的 **参数** 是 1000。
* rm 命令 -r **参数**用来删除目录。

但在看英文文档时，经常会交替的出现`argument`，`parameter`以及`option`，尤其是argument和parameter，更让我困惑，应该不只是同义词这么简单吧？于是我特地查阅了一些资料。主要针对Java和Shell下的语义进行了梳理：


<!--more-->


# Java中的argument和parameter
Java语境中的argument和parameter，在官方文档中给予了非常明确的说明。Oracle Java官方教程的 [Passing Information to a Method or a Constructor](https://docs.oracle.com/javase/tutorial/java/javaOO/arguments.html) 一节，提到：
> Note: Parameters refers to the list of variables **in a method declaration**. Arguments are the **actual values that are passed** in when the method is invoked. When you invoke a method, the arguments used must match the declaration's parameters in type and order.

所以，**在Java中，parameter指的是函数定义。而argument指的函数调用**。

因此，在Java Doc中，使用的是`@param`注解来说明参数含义：
```java
    /**
     * @param  initialCapacity the initial capacity.
     * @throws IllegalArgumentException if the initial capacity is negative.
     */
    public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }
```

而用`IllegalArgumentException`表示调用时传递的参数不合法。
```java
/**
 * Thrown to indicate that a method has been passed an illegal or
 inappropriate argument.
 *
 * @author  unascribed
 * @since   JDK1.0
 */
public class IllegalArgumentException extends RuntimeException {
```

另外，Java语境中的含义，也是大多数编程语言中的含义，这一点在维基百科 [Parameter (computer programming) - Wikipedia](https://en.wikipedia.org/wiki/Parameter_%28computer_programming%29) 中有说明：

> The term parameter (sometimes called formal parameter) is often used to refer to the variable as found in the function definition, while argument (sometimes called actual parameter) refers to the actual input supplied at function call. 

维基百科也用形式参数（formal parameter）指代parameter，而argument则对应于实际参数（actual parameter）。

# Shell中的argument、option和parameter

## argument
Shell中，无论是命令、脚本或函数，都无法像Java那样定义参数，所以也就不存在Java中严格意义的parameter了。事实上，命令行的功能太复杂，组合太多，根本无法像单一功能的Java函数那样明确的parameter。虽然如此，argument还是类似的，指运行期传入的值。比如bash的man文档里有这样的说明：
> SHELL GRAMMAR
   Simple Commands
       A  simple  command  is  a sequence of optional variable assignments followed by blank-separated words and redirections, and terminated by a control operator.  The first word specifies the command to be executed, and is passed as **argument zero**.  The remaining words are **passed as arguments** to the invoked command.

具体到执行阶段，除了第一个单词是执行的命令或函数，后面用空格分隔的单词都称作argument。比如`ls -l /tmp`，一共有2个argument：`-l`和`/tmp`。

不过管道符或重定向都不能算作argument，本质上它们不属于前面的命令。所以不能说`ls -l /tmp >files.txt`里的`>files.txt`是第3个argument。

## option
option，是具体程序自己定义和识别，一个程序接受那些option是程序里写死的，所以option指在具体的命令或函数下有意义。option可以看做对argument的细分，它们一般是带`-`或`--`的argument。这样便于程序去解析。所以`ls -l /tmp`的第2个argument是一个option。

维基百科的[Command-line interface](https://en.wikipedia.org/wiki/Command-line_interface#Command-line_option)，对option是这样描述的
> A **command-line option** or simply **option** (also known as a **flag** or **switch**) modifies the operation of a command; the effect is determined by the command's program. 

## parameter
如果非要和Java中的parameter对应，可以认为Shell中是**位置参数（Positional Parameters）**，第1个参数，第2个参数等等，非常简单粗暴。至于它们是什么含义，那是程序实现的问题了，这也造成了命令行或shell函数需要自己`解析`位置参数。简单的好处就是包容性强。

不过，bash文档里，还是有专门对parameter说明的一节：
> A parameter is an entity that stores values.  It can be a name, a number, or one of the special characters listed below under Special Parameters.  A variable is a parameter denoted by a name. 

而在parameter下又细分为：位置参数（Positional Parameters）、特殊参数（Special Parameters，如`$#`、`$?`）、shell变量（Shell Variables，即又shell自动设置或使用的变量，比如`PATH`）以及数组（Arrays）。这里的parameter更像是特殊的变量。

## 更实用的区分
Shell上面的解释，有点太晦涩，而且不实用。倒是Stack Overflow上有个解释：[bash - Difference between terms: “option”, “argument”, and “parameter”? - Stack Overflow](https://stackoverflow.com/questions/36495669/difference-between-terms-option-argument-and-parameter)，虽然不准确，但更实用。他认为：
> A parameter is an argument that provides information to either the command or one of its options

在这个解释下，是这样区分的：

* argument是命令后传递的所有东西的统称。（这一点和开始的解释一样）
* argument中有一种是option，它们以`-`或`--`开头。（这一点和开始的解释一样）
* option接受的值称作parameter（或者称作value也罢）。比如`sort -k 1`，1就是option的parameter，表示排序的列是第一列。

# 总结
* 在不严格的情况下，parameter和argument是可以混用的。
* Java或通常的编程语言中，parameter指代函数声明中的变量；而argument指代函数被调用时传递的实际输入。
* 在Shell或命令行中，argument的含义和Java是类似的，指代调用或运行时的输入值。但parameter的含义有点晦涩和不实用。若干结合option，一个简单而实用的区分是：argument是统称，option是`--`或`-`开头的argument，而parameter看成option的值。

# 参考
1. [Passing Information to a Method or a Constructor (The Java™ Tutorials _ Learning the Java Language _ Classes and Objects)](https://docs.oracle.com/javase/tutorial/java/javaOO/arguments.html)
2. [Parameter (computer programming) - Wikipedia](https://en.wikipedia.org/wiki/Parameter_%28computer_programming%29)
3. [Command-line interface - Wikipedia](https://en.wikipedia.org/wiki/Command-line_interface#Command-line_option)
4. [bash - Difference between terms: “option”, “argument”, and “parameter”? - Stack Overflow](https://stackoverflow.com/questions/36495669/difference-between-terms-option-argument-and-parameter)

> 本文转自我的知乎专栏[《Java与Linux学习周刊》](https://zhuanlan.zhihu.com/java-linux)的[《第2期：argument、parameter以及option有什么区别？》](https://zhuanlan.zhihu.com/p/74477584)。
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg0MjQ4MDQyN119
-->
