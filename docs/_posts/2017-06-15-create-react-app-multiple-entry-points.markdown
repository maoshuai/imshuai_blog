---
layout: post
title: 修改create-react-app支持多入口
date: '2017-06-15 11:59:53'
tags:
- react
---

使用Facebook官方脚手架[create-react-app](https://github.com/facebookincubator/create-react-app)创建React应用，默认只能生成一个SPA，入口是index.html。虽然，SPA的页面切换可以使用前台路由框架方便（比如React-Router）实现，这也是SPA的推荐做法；但某些情况下，仍要将页面切分为多个页面，或者在同一个工程开发多个SPA，比如一个是面向客户的SPA，一个是后台管理的SPA。

(==For English readers at article bottom==)

我搜索了一下，网上确实有不少人有类似需求：[Possible to have multiple entry points? #1079](https://github.com/facebookincubator/create-react-app/issues/1079)，官方给出的回答是：

>Sorry, but Create React App doesn't support this use case.
You can eject and then configure Webpack to have multiple entry points.

查阅create-react-app和webpack官方文档，测试如下方式可行，步骤如下：（假设在默认的index.html外增加一个admin.html）

# 1. Eject
要实现自定义配置，就需要先Eject出配置（此步骤对create-react-app工程不可逆）：
```
npm run eject
```
运行后，package.js会被更新，工程下会多出config目录，其中有webpack有两个配置文件，分别对应开发和生产环境（/config/webpack.config.dev.js和/config/webpack.config.prod.js）。这两个配置文件都要修改，但略有不同，下面以dev为例说明：

# 2. 修改webpack配置支持多入口
由于是使用webpack打包，先要让webpack配置出多入口。/config/webpack.config.dev.js默认配置的入口如下：
```JavaScript
 entry: [
    require.resolve('react-dev-utils/webpackHotDevClient'),
    require.resolve('./polyfills'),
    require.resolve('react-error-overlay'),
    paths.appIndexJs,
  ],
  output: {
    path: paths.appBuild,
    pathinfo: true,
    filename: 'static/js/bundle.js',
    chunkFilename: 'static/js/[name].chunk.js',
    publicPath: publicPath,
    devtoolModuleFilenameTemplate: info =>
      path.resolve(info.absoluteResourcePath),
  },
```

根据webpack官方文档[MULTIPLE ENTRY POINTS](https://webpack.github.io/docs/multiple-entry-points.html)，可做如下修改：

要点是：

1. entry从原来的数组扩展为==对象==，每个key代表一个入口。
2. output中的filename要区分输出名，可增加[name]变量，这样会根据entry分别编译出每个entry的js文件。
3. 由于`path`里面没有定义新的entry的路径，图方便可以直接写死为`paths.appSrc + "/admin.js"`
```
  entry: {
    index: [
      require.resolve('react-dev-utils/webpackHotDevClient'),
      require.resolve('./polyfills'),
      require.resolve('react-error-overlay'),
      paths.appIndexJs,
    ],
    admin:[
      require.resolve('react-dev-utils/webpackHotDevClient'),
      require.resolve('./polyfills'),
      require.resolve('react-error-overlay'),
      paths.appSrc + "/admin.js",
      ]
  },
  output: {
    path: paths.appBuild,
    pathinfo: true,
    filename: 'static/js/[name].bundle.js',
    chunkFilename: 'static/js/[name].chunk.js',
    publicPath: publicPath,
    devtoolModuleFilenameTemplate: info =>
      path.resolve(info.absoluteResourcePath),
  },
```
这样在src文件夹下，就可以再增加一个admin.js的入口，单独写新的SPA。

普通的webpack打包工程，到此即可实现多入口，但create-react-app流程更复杂，需继续修改。

# 3. 修改HtmlWebpackPlugin生成多个HTML
Webpack配置多入口后，只是编译出多个入口的JS，同时入口的HTML文件由HtmlWebpackPlugin生成，也需做配置。

原配置如下：
```
   new HtmlWebpackPlugin({
      inject: true,
      template: paths.appHtml,
    }),
```
修改为：
```
    new HtmlWebpackPlugin({
      inject: true,
      chunks: ["index"],
      template: paths.appHtml,
    }),
    new HtmlWebpackPlugin({
      inject: true,
      chunks: ["admin"],
      template: paths.appHtml,
      filename: 'admin.html',
    }),
```
每调一次HtmlWebpackPlugin生成一次HTML页面，故为admin.html多增加一个节点。其他要点如下：

* `chunks`，指明哪些webpack入口的JS会被注入到这个HTML页面。如果不配置，则将所有entry的JS文件都注入HTML。
* `filename`，指明生成的HTML路径，如果不配置就是build/index.html，admin配置了新的filename，避免与第一个入口的index.html相互覆盖。

另外，`template`属性也可以修改为不同的HTML模板，这里的例子，我么就和index入口共用HTML模板了。

# 4. 修改webpack Dev Server配置

上述配置做完后，理论就可以打包出多入口的版本；但使用`npm start`启动后，发现无论输入/index.html还是/admin.html，好像都是和原来/index.html显示一样的内容。甚至输入显然不存在的/xxxx.html，也显示为/index.html的内容。

这种现象，初步判断是HTTP服务器把所有请求重定向到了/index.html。对于单页应用，这种做法是没有问题的（本来就一个页面）；但我们新增的/admin.html就可以访问了。

参考官方文档[The historyApiFallback option](https://webpack.github.io/docs/webpack-dev-server.html#the-historyapifallback-option)，发现是webpack dev server的问题，还要额外做一些配置，需修改/config/webpackDevServer.config.js。


原配置如下：
```
 historyApiFallback: {
      disableDotRule: true,
    },
```
修改为：
```
    historyApiFallback: {
      disableDotRule: true,
      // 指明哪些路径映射到哪个html
      rewrites: [
        { from: /^\/admin.html/, to: '/build/admin.html' },
      ]
    }
```
增加的rewrites节点，特别对/admin.html这个URL重定向为/build/admin.html页面（也就是HtmlWebpackPlugin输出的HTML文件路径），这样/admin.html就可以正常访问了。
至此，dev环境的多入口问题就解决了。

# prod环境
prod环境，比dev环境更简单。由于不存在webpack Dev Server，直接在config/webpack.config.prod.js同理做2和3步骤即可。

使用`npm run build`构建prod版本，观察build目录，入口/admin.html的html、js和css文件俱在：
```
build
├── admin.html
├── asset-manifest.json
├── favicon.ico
├── index.html
├── manifest.json
├── service-worker.js
└── static
    ├── css
    │   ├── admin.d41d8cd9.css
    │   ├── admin.d41d8cd9.css.map
    │   ├── index.9a0fe4f1.css
    │   └── index.9a0fe4f1.css.map
    ├── js
    │   ├── admin.f3dca2cd.js
    │   ├── admin.f3dca2cd.js.map
    │   ├── index.4b87195c.js
    │   └── index.4b87195c.js.map
    └── media
        └── logo.5d5d9eef.svg
```

# 优化
如果每增加一个入口，都要配置一番，显然太麻烦，是否可以改造一下配置，更方便快速的实现呢，后续再研究一下？


# ==For English Readers==
### Four steps to add multiple entries to create-react-app 
(say we add an admin.html):

### 1. Eject the project
```
npm run eject  
```
### 2. Add multiple entry to webpack.config.dev.js
```
entry: {
    index: [
      require.resolve('react-dev-utils/webpackHotDevClient'),
      require.resolve('./polyfills'),
      require.resolve('react-error-overlay'),
      paths.appIndexJs,
    ],
    admin:[
      require.resolve('react-dev-utils/webpackHotDevClient'),
      require.resolve('./polyfills'),
      require.resolve('react-error-overlay'),
      paths.appSrc + "/admin.js",
      ]
  },
  output: {
    path: paths.appBuild,
    pathinfo: true,
    filename: 'static/js/[name].bundle.js',
    chunkFilename: 'static/js/[name].chunk.js',
    publicPath: publicPath,
    devtoolModuleFilenameTemplate: info =>
      path.resolve(info.absoluteResourcePath),
  },
```
### 3. Modify HtmlWebpackPlugin
add a new plugin node:
```
    new HtmlWebpackPlugin({
      inject: true,
      chunks: ["index"],
      template: paths.appHtml,
    }),
    new HtmlWebpackPlugin({
      inject: true,
      chunks: ["admin"],
      template: paths.appHtml,
      filename: 'admin.html',
    }),
```

### 4. webpack Dev Server
rewrite urls
/config/webpackDevServer.config.js:
```
    historyApiFallback: {
      disableDotRule: true,
      // 指明哪些路径映射到哪个html
      rewrites: [
        { from: /^\/admin.html/, to: '/build/admin.html' },
      ]
    }
```

