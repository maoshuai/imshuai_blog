---
class: post-template
navigation: True
title: 第1期：抛开IDE，了解一下javac如何编译
date: '2019-7-25 20:25:23'
tags:
- Java与Linux学习周刊
---

IDE或maven等工具已将Java程序的编译代劳。但工具越高级，隐藏的细节就越多，一旦出现问题就懵逼，归根到底还是基础概念不牢靠。返璞归真，回到最原始的地方`javac`，会让问题豁然开朗。下面就一步一步演示用`javac`和`java`徒手编译运行一个常规工程。


<!--more-->


# Hello World练个手

来个简单的先，我们祭出祖传的HelloWorld程序。（感兴趣的话，可以试一试徒手是否写的出来~）

```java
public class HelloWorld{
	public static void main(String[] args){
		System.out.println("Hello, World!");
	}
}
```

写完后，保存为：`HelloWorld.java`，然后在当前目录执行`javac`编译命令：
```shell
javac HelloWorld.java
```

查看**当前目录**（更准确的说是java文件同级目录），果然生成了`HelloWorld.class`：
```shell
maoshuai@ms:~/javaLinux/w1$ ls
HelloWorld.class  HelloWorld.java
```

继续在**当前目录运行**`java`命令，正确打印出Hello, World!

```shell
maoshuai@ms:~/javaLinux/w1$ java HelloWorld 
Hello, World!
```

老司机，稳！看起来很简单嘛：先`javac`再`java`。

虽然简单，但新手通常会犯的一个错：想象成去**执行**`.class`文件，比如写成这样，自然会报错：

```shell
maoshuai@ms:~/javaLinux/w1$ java HelloWorld.class
Error: Could not find or load main class HelloWorld.class
```

 需要明白，`java`的参数，**传入的是main函数所在的类的名字，而不是class文件；java会根据类名自动去找class文件**。

# 带个包名
一切都很顺利，但没有包名是不专业的，所以我们加一个牛逼的包`com.imshuai.javalinux`：

```java
package com.imshuai.javalinux;
public class HelloWorld{
	public static void main(String[] args){
		System.out.println("Hello, World!");
	}
}
```

还是一样用`javac`编译，查看**当前目录**下`HelloWorld.class`生成了，很顺利。

还是一样用`java`命令，瞬间被打脸：

```shell
maoshuai@ms:~/javaLinux/w1$ java HelloWorld 
Error: Could not find or load main class HelloWorld
```

想了想，**HelloWorld已经有自己的包名了，所以它的名字不在是没有姓氏**的`HelloWorld`，新名字叫`com.imshuai.javalinux.HelloWorld`，那么传给`java`自然要用新名字，再试一试：

```shell
maoshuai@ms:~/javaLinux/w1$ java com.imshuai.javalinux.HelloWorld
Error: Could not find or load main class com.imshuai.javalinux.HelloWorld
```

还是被打脸，这时候老司机告诉你，创建一个`com/imshuai/javalinux`目录，然后把`HelloWorld.class`放进来，执行：

```shell
maoshuai@ms:~/javaLinux/w1$ mkdir -p com/imshuai/javalinux
maoshuai@ms:~/javaLinux/w1$ mv HelloWorld.class com/imshuai/javalinux
maoshuai@ms:~/javaLinux/w1$ java com.imshuai.javalinux.HelloWorld
Hello, World!
```

果然，正常打印出了Hello, World!

**上面的步骤，说明了两点：**
1. 增加了package名，所以class名也变了，行不改名坐不改姓，自然要带上姓（即所谓全限定名）。
2. Java **会根据包名对应出目录结构，并从class path搜索该目录去找class文件**。由于默认的class path是当前目录，所以`com.imshuai.javalinux.HelloWorld`必须存储在`./com/imshuai/javalinux/HelloWorld.class`

当然每次自己创建包路径的目录太麻烦。**`-d`参数可以代劳上面的工作**：
```shell
maoshuai@ms:~/javaLinux/w1$ javac -d . HelloWorld.java 
maoshuai@ms:~/javaLinux/w1$ ls
com  HelloWorld.java
maoshuai@ms:~/javaLinux/w1$ java com.imshuai.javalinux.HelloWorld
Hello, World!
```
`-d`指定了生成class文件的根目录（这里用的是当前目录），并且会根据class的包路径创建子目录。

# 编译两个有依赖关系的class

包名解决了，我们再复杂些，搞个依赖调用。首先，我们抽取一个`HelloService`：

```java
package com.imshuai.javalinux;
public class HelloService{
	public void printHello(String name){
		System.out.println("Hello, " + name + "!");
	}
}
```

然后修改`HelloWorld.java`，调用`HelloService`完成say hello：

```java
package com.imshuai.javalinux;
public class HelloWorld{
	public static void main(String[] args){
		HelloService service = new HelloService();
		service.printHello("World");
	}
}
```

接着我们依次编译：`HelloService.java `和`HelloWorld.java`，最后运行：
```shell
maoshuai@ms:~/javaLinux/w1$ javac -d . HelloService.java 
maoshuai@ms:~/javaLinux/w1$ javac -d . HelloWorld.java 
maoshuai@ms:~/javaLinux/w1$ ls
com  HelloService.java  HelloWorld.java
maoshuai@ms:~/javaLinux/w1$ java com.imshuai.javalinux.HelloWorld
Hello, World!
```

直觉上，要先编译`HelloService.java`，这是对的。那如果先编译`HelloWorld.java `呢？当然是打脸：

```shell
maoshuai@ms:~/javaLinux/w1$ javac -d . HelloWorld.java 
HelloWorld.java:4: error: cannot find symbol
		HelloService service = new HelloService();
		^
  symbol:   class HelloService
  location: class HelloWorld
HelloWorld.java:4: error: cannot find symbol
		HelloService service = new HelloService();
		                           ^
  symbol:   class HelloService
  location: class HelloWorld
2 errors
```

如果编译的时候，还要根据依赖关系确定顺序，太low了吧。我觉得`java`命令应该能自动解决它，一次性将两个java文件传给它试一试：

```shell
maoshuai@ms:~/javaLinux/w1$ javac -d . HelloWorld.java HelloService.java 
maoshuai@ms:~/javaLinux/w1$ ls
com  HelloService.java  HelloWorld.java
maoshuai@ms:~/javaLinux/w1$ java com.imshuai.javalinux.HelloWorld
Hello, World!
```

牛逼，它自动解决了顺序问题，赞一个（虽然我不怀好意的将`HelloWorld.java`放到了前面）！

# 使用src和target目录
从上面的例子可以看出，虽然class文件必须放在包名一致的目录里，**但java源文件并没有这个要求**。不过，为了管理方便，我们将java源文件也放在包结构目录里：

```shell
maoshuai@ms:~/javaLinux/w1$ mkdir -p com/imshuai/javalinux
maoshuai@ms:~/javaLinux/w1$ mv *.java com/imshuai/javalinux/
maoshuai@ms:~/javaLinux/w1$ ls com/imshuai/javalinux/
HelloService.java  HelloWorld.java
maoshuai@ms:~/javaLinux/w1$ javac -d . com/imshuai/javalinux/*.java
maoshuai@ms:~/javaLinux/w1$ ls com/imshuai/javalinux/
HelloService.class  HelloService.java  HelloWorld.class  HelloWorld.java
maoshuai@ms:~/javaLinux/w1$ java com.imshuai.javalinux.HelloWorld
Hello, World!
```

编译时`javac`要传入新的java文件路径（这里用了通配符），其他也没有什么不同。可以看到**class文件生成到了与java文件相同的目录里**。class文件和java源文件放在一起，很不清爽，能否像IDE里那样：java文件放到src目录，class文件放到target目录？下面我试一试。

先创建src和target目录，并将原来的java文件都移动到src目录：
```shell
maoshuai@ms:~/javaLinux/w1$ mkdir src
maoshuai@ms:~/javaLinux/w1$ mkdir target
maoshuai@ms:~/javaLinux/w1$ mv com src
maoshuai@ms:~/javaLinux/w1$ ls
src  target
```

然后编译，`-d`参数指定到target目录：
```shell
maoshuai@ms:~/javaLinux/w1$ javac -d target src/com/imshuai/javalinux/*.java
maoshuai@ms:~/javaLinux/w1$ ls target/com/imshuai/javalinux/
HelloService.class  HelloWorld.class
```
怎么运行呢？直接在当前目录运行是不行了，毕竟多了一层target目录，进入target目录运行，妥妥的：
```shell
maoshuai@ms:~/javaLinux/w1/target$ java com.imshuai.javalinux.HelloWorld
Hello, World!
```

除了进入`target`目录以外，更常用的方法是通过`-classpath`（或简写为`-cp`）选项设置**类路径**：
```
maoshuai@ms:~/javaLinux/w1$ java -cp target com.imshuai.javalinux.HelloWorld
Hello, World!
```

# 类路径CLASSPATH

上面演示了通过`-cp`设置类路径。下面再进一步研究一下类路径。

类路径，是JRE搜索用户级class文件或其他资源的路径，`javac`或`java`等工具都可以指定类路径。如果没有设置，默认的类路径就是当前目录。**但如果设置了类路径，默认值就被覆盖了，所以如果想保留当前目录为类路径，需要同时将`.`加入**，有点像默认构造函数的感觉。

类路径，可以通过环境变量`CLASSPATH`或`-cp`参数设置，后者会覆盖前者。推荐通过`-cp`设置，它只会影响当前进程。

类路径类似操作系统里的`path`概念，不过它是java工具搜索class文件的路径。同样的，类路径可以是多个，并通过分号分隔：

```
export CLASSPATH=path1:path2:...
```

或者：
```
sdkTool -classpath path1:path2:...
```
sdkTool可以是 java, javac, javadoc等。

类路径不仅可以是目录，还也可以是jar包或zip包。

类路径的设置是有顺序的，java会优先在靠前的类路径里搜索。这一点和操作系统的`path`类似。

类路径可以用通配符`*`匹配jar或zip，但
1. 通配符只匹配jar或zip，比如path/*只是将下面的jar或zip加入类路径，但path本身不加入类路径。
2. 通配符不递归搜索，即指匹配第一层目录下的jar或zip。
3. 通配符匹配到的jar或zip，加入到classpath的顺序是不确定的。因此，更稳妥的做法是显示的枚举所有jar或zip。
4. 通配符适用于`CLASSPATH`变量或`-cp`参数，但不适用于jar包的manifest文件。

# 更真实的场景

下面的java项目有较多的包结构，`还有jar包依赖`，如何编译呢？
(工程代码下载：https://github.com/maoshuai/java-linux-weekly/tree/master/attachment/w1)
```shell
├── lib
│   ├── logback-classic-1.2.3.jar
│   ├── logback-core-1.2.3.jar
│   └── slf4j-api-1.7.26.jar
├── resources
│   └── logback.xml
├── src
│   └── com
│       └── imshuai
│           └── javalinux
│               ├── HelloWorld.java
│               └── service
│                   ├── IGreetingService.java
│                   └── impl
│                       ├── AlienGreetingService.java
│                       ├── CatGreetingService.java
│                       ├── DogGreetingService.java
│                       └── HumanGreetingService.java
└── target
```

最直接的办法，跟刚才一样，只不过体力活多一些，要枚举所有的java文件，同时使用通配符将lib下的jar加入类路径：

```shell
javac \
-cp "lib/*" \
-d target \
src/com/imshuai/javalinux/HelloWorld.java \
src/com/imshuai/javalinux/service/IGreetingService.java \
src/com/imshuai/javalinux/service/impl/*.java
```

编译成功，`java`命令运行一下（注意：target和lib下的jar都需要加入类路径）：

```shell
maoshuai@ms:~/javaLinux/w1$ java -cp "target:lib/*" com.imshuai.javalinux.HelloWorld XiaoMing
22:16:15.887 [main] INFO HumanGreetingService - XiaoMing is saying hello: Ni Chou Sha!
```

如果文件很多，手工列出这些文件不太现实，可以通过`find`命令完成：

```shell
javac -cp "lib/*" -d target $(find src -name "*.java")
```

`javac`还提供了一种**列表文件**的办法，即将要编译的java文件列表写到一个文本文件里，我们用find命令完成：

```shell
maoshuai@ms:~/javaLinux/w1$ find src -name "*.java" >javaFiles.txt
```

生成的javaFiles.txt内容如下：
```shell
src/com/imshuai/javalinux/service/IGreetingService.java
src/com/imshuai/javalinux/service/impl/HumanGreetingService.java
src/com/imshuai/javalinux/service/impl/CatGreetingService.java
src/com/imshuai/javalinux/service/impl/DogGreetingService.java
src/com/imshuai/javalinux/service/impl/AlienGreetingService.java
src/com/imshuai/javalinux/HelloWorld.java
```
然后用`@`开头的`@javaFiles.txt`，代表传给`javac`的是列表文件名：
```shell
javac -cp "lib/*" -d target @javaFiles.txt
```

不仅如此，参数也可以放入文件（注意：`-cp`不能放进去）。比如`javaFiles.txt`中加入`-d target`
```shell
-d target
src/com/imshuai/javalinux/service/IGreetingService.java
src/com/imshuai/javalinux/service/impl/HumanGreetingService.java
src/com/imshuai/javalinux/service/impl/CatGreetingService.java
src/com/imshuai/javalinux/service/impl/DogGreetingService.java
src/com/imshuai/javalinux/service/impl/AlienGreetingService.java
src/com/imshuai/javalinux/HelloWorld.java
```
这样只用执行：
```shell
javac -cp "lib/*"  @javaFiles.txt
```

不过为了清晰，我们可以把参数`-d target`单独放到一个文件`javaOptions.txt`，然后传两个@文件：

```shell
javac -cp "lib/*" @javaOptions.txt  @javaFiles.txt
```
**使用列表文件的好处是，规避了命令行参数长度的限制，并且可以在任何操作系统上运行**。

有了上面的准备，我们可以写出一个自动化编译的脚本了：
```shell
PROJECT_DIR=/home/maoshuai/javaLinux/w1
# clean target directory
rm -rf $PROJECT_DIR/target/*
# prepare arg files
find $PROJECT_DIR/src -name "*.java">$PROJECT_DIR/target/javaFiles.txt
echo "-d $PROJECT_DIR/target" >$PROJECT_DIR/target/javaOptions.txt
# compile
javac -cp "$PROJECT_DIR/lib/*" @$PROJECT_DIR/target/javaOptions.txt @$PROJECT_DIR/target/javaFiles.txt
# copy resources to target
cp -rf $PROJECT_DIR/resources/* $PROJECT_DIR/target
# clean temp files
rm -rf $PROJECT_DIR/target/javaOptions.txt $PROJECT_DIR/target/javaFiles.txt
```

# 是时候仔细看一下javac了
Oracle的[官方文档介绍`javac`如下](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/javac.html)：
> Reads Java class and interface definitions and compiles them into bytecode and class files.

javac的语法如下：
```shell
javac [ options ] [ sourcefiles ] [ classes] [ @argfiles ]
```
* options：是一些参数，比如-cp，-d
* sourcefiles：就是编译的java文件，如`HelloWorld.java`，可以是多个，并用空格隔开
* classes：用来处理处理注解。暂时没搞懂怎么用
* @argfiles，就是包含option或java文件列表的文件路径，用@符号开头，就像上面的@javaOptions.txt和@javaFiles.txt

# 总结
将`javac`的基本用法总结如下：
1. `-cp`参数设置类路径，基本用法是将编译时依赖的jar包加入类路径。并可用`*`通配jar包。
2. `-d` 参数用来设置class文件编译到单独目录，并根据包名创建子目录。
3. 理论上将java文件的路径全部传给`javac`即可，但操作上，可以通过find命令将文件列表输出到文件中，通过@argfiles参数传递。

# 参考
1. https://docs.oracle.com/javase/8/docs/technotes/tools/unix/javac.html
2. https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html
3. https://docs.oracle.com/javase/8/docs/technotes/tools/unix/classpath.html
4. https://docs.oracle.com/javase/tutorial/getStarted/problems/index.html
5. http://docs.oracle.com/javase/8/docs/technotes/tools/findingclasses.html


> 本文转自我的知乎专栏[《Java与Linux学习周刊》](https://zhuanlan.zhihu.com/java-linux)的[《第1期：抛开IDE，了解一下javac如何编译》](https://zhuanlan.zhihu.com/p/74229762)。
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDQ2MTQ0MTgwXX0=
-->
