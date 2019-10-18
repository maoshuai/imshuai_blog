---
title: Jupyter Notebook简介和配置说明
date: '2017-08-31 13:54:39'
tags:
- tools
---

学习Python的时候，为了快速验证我们学习的知识点，可以用Python自带的python命令进入REPL交互界面，输入Python语句。但实在不方便：

1. 命令行环境，输错了一句就要重来（比如def一个函数）
2. 如果某段逻辑写的有误，需要重新执行一遍所有代码。

所以代码长了，我们写成.py文件执行，但还是不得劲儿。

ipython倒是一个很不错的REPL，除了像python命令一样可以执行Python语句外，还会保留每一步输出，也是很多书籍和教程里面使用的工具，但还不够好。

直到我发现了jupyter notebook神器：http://jupyter.org/ ，它几乎满足了我所有的幻想！

如果用一句话描述，那就是：jupyter notebook==就像一张“草稿纸”一样==。

它的样子是这样的：
![jupyter notebook snapshot](https://cdn.imshuai.com/images/2017/08/jupyter-snapshot.png)

它可以记录你的所有命令和输出结果，每个命令都可以方便修改执行；整个“演算过程”可以直接保存成一个.ipynb文件，直接分享给另一个有jupyter环境的用户==完整再现你的演算过程==。

### 1. 支持markdown
原生支持markdown语法，这在演算或写教程的时候特别有用：代码和文档混合在一起：

1. 方便的文档混写
2. 方便的代码编辑

并且，由于是图形环境，对诸如matplolib等图表工具的支持良好，几乎可以当做“免费版matlab使用”

### 2. 丰富的内核（kernel）扩展
这还没完，除原生支持Python外，jupyter还可以通过扩展“内核（kernel）”支持你能想到的任何绝大多数语言，到[Jupyter kernels](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)上，可以查看其支持的语言列表，俨然成了一个生态，比如知名的有：

1. JavaScript
2. Haskell
3. Ruby
4. Go
5. Scala
6. PHP

### 3. GitHub支持其文件格式
jupyter生成的文件，github也直接支持预览，这一点也很赞。

### 4. 在线尝试jupyter

想尝试的话，可以通过以下途径：

* 官方demo

在上面测试：https://try.jupyter.org/

* 量化投资平台

另外，很多量化投资平台，也都是基于jupyter notebook搭建的，大家在上面写量化策略的
Python代码，进行策略验证。比如国内知名的：

* [聚宽](https://www.joinquant.com/)
* [优矿](https://uqer.io/)
* [米筐](https://www.ricequant.com/)

### 5. 配置jupyter notebook

* 第一次启动jupyter

安装完毕后，执行`jupyter notebook`，即可快速启动jupyter，一般直接会弹出个浏览器页面，地址类似：

`http://localhost:8888/?token=1bba445e9540f7d40e99e9e3b59dfe78212adfba0d6ae329`

token是临时生成的字符串，可以直接登录进入。然而这种方式并不方便：

1. 没有固定的登录密码
2. 几乎没有配置（当然可以输入到命令行参数，但每次启动都要输入，太麻烦）

就像你想到的那样，肯定有配置文件。
 
* 添加配置文件

执行命令
```
 jupyter notebook --generate-config
```
按照国际惯例，该命令会在用户目录下创建一个配置目录，名字就像你猜的一样，就是`.jupyter`，目录里会看到一个`jupyter_notebook_config.py`的配置文件。


* 编辑配置文件

主要的配置项：
```
# 登录密码，默认没有密码，所以每次启动服务器后都会产生一个随机数token，配置了密码后就不用每次使用随机数token了
c.NotebookApp.password = ''

## 服务的端口，用默认的8888即可
c.NotebookApp.port = 8888

## 是否需要自动弹出浏览器，服务器端一般不需要
c.NotebookApp.open_browser = False

## The directory to use for notebooks and kernels.
## 不设置的话就是启动命令所在的目录
c.NotebookApp.notebook_dir = '/home/maoshuai/work'
```

如果要去远程可访问，还要打开ip限制（默认jupyter notebook只能本机访问）：
```
c.NotebookApp.ip='*'
```

* 密码生成

需要注意的是，上面的c.NotebookApp.password配置的并==非明文密码==，按照配置文件上的注释步骤生成：

1. 在命令行下输入ipython，进入REPL环境
2. 执行：`from notebook.auth import passwd;passwd()`
3. 根据提示输入明文密码
4. 生成hash后的密码类似如下：
```
In [2]: from notebook.auth import passwd; passwd()
Enter password:
Verify password:
Out[2]: 'sha1:e4ac9ea2e432:ce17c208cac9c15c59dd6f34ffe2a262f6d65bf3'
```
将sha1开头的一串字符，拷贝到c.NotebookApp.password字段即可。这样再次启动jupyter时，就有了密码输入界面。


### 6. 总结
以上就是jupyter notebook的简介，非常推荐。什么？没安装指南？这还要问，自己到官网上看啊！
