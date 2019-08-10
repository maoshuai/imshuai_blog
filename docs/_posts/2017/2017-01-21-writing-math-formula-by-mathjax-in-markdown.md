---
layout: postclass: post-templatenavigation: True
title: 使用MathJax在Ghost博客插入数学公式
date: '2017-01-21 02:53:03'
tags:
- ghost-blog
---

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

Markdown文档，没有办法直接书写数学公式。好在有很多方案可以将LaTeX语法的公式渲染出来。


[MathJax](https://www.mathjax.org)™，是一个优秀的JavaScript数学公式生成引擎，可以在浏览器中，对输入的数学公式进行漂亮的渲染，比如这样：

$$x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}$$

### MathJax的特点

1. 使用CSS和Web fonts或者SVG实时渲染，而不是图片，因此在缩放上具有“矢量”优势。
1. 适用于浏览器，只用引入一个js文件即可实现。
1. 缺点是，过多的数学公式，可能会降低页面的加载速度。

由于Markdown可以引入HTML语法，因此MathJax能直接用于Markdown文档，尤其是Ghost这种在线博客。

### 在HTML文档中引用MathJax
在HTML引入如下JavaScript
```
<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
```
上面使用的是官方CDN，也可以下载js文件到自己的服务器进行引用

### 在Ghost中引用MathJax
由于MathJax会对页面加载速度有一定的性能损失，故不建议在整个博客中进行加载。可以在需要使用数学公式的Markdown文档的开通，直接引入即可。


