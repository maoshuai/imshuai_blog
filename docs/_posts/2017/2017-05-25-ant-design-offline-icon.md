---
title: 内网环境Ant Design使用离线Icon图标资源
date: '2017-05-25 13:40:53'
tags:
- react
- ant-design
---

最近在使用React实现公司一套系统的前端。控件库，看中了蚂蚁金服的[Ant Design](https://ant.design)。


<!--more-->


脚手架使用的是React官方的[create-react-app](https://github.com/facebookincubator/create-react-app)，创建完成后，引入了Ant Design。在我自己的笔记本调试的都很好，但放到公司的开发机器上，发现图标资源都无法加载。马上看了一下浏览器的资源请求情况，果然，Ant Design默认使用的是阿里的CDN。

公司是内网环境，显然是行不通的。官方文档果然给出了[本地部署的提示](https://ant.design/components/icon-cn/#本地部署)。

研究了一番，发现主要是要覆盖`@icon-url`这个less变量，所以就要开启less支持。幸好官方在[自定义主题](https://ant.design/docs/react/use-with-create-react-app-cn#自定义主题)一节，已经给出了create-react-app中如何实现主题less变量的覆盖；同理，在modifyVar节点增加`@icon-url`的覆盖，如下：
```
 modifyVars: { 
          // 修改整体主题颜色
          // "@primary-color": "#1DA57A",  
          // 修改图标库为本地离线，而不是阿里云CDN上的图标资源 
          "@icon-url": '"/iconfont/iconfont"' },
```
然后在`/public`目录增加`iconfront`目录，并将下载好的[Web Font文件](https://ant.design/docs/resource/download)放到这个目录即可。
不过还是有几个细节要注意：

1. 要同时修改`webpack.config.dev.js`和`webpack.config.prd.js`，保证测试环境和生产环境都生效。
2. `iconfront`文件夹要放在`/public`目录，而不是`/src`目录。虽然调试环境两者都能生效，但生产环境后者是不生效的。
3. `@icon-url`的值里面有双引号。
