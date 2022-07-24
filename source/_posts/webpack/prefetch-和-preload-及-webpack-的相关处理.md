---
title: prefetch 和 preload 及 webpack 的相关处理
date: 2021-03-11 21:12:40
categories: webpack
---

使用预取和预加载是网站性能和用户体验提升的一个很好的途径，本文介绍了使用 prefetch 和 prefetch 进行预取和预加载的方法，并使用 webpack 进行实现

## Link 的链接类型

[`<link>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link) 标签的 `rel` 属性可以定义链接类型，`prefetch` 是其中的一种，与 `href` 配合使用可以预取或预加载对应资源

```
<link rel="prefetch" herf="URL">

```

`preload` 是另外一种类型，同样用 href 定义资源地址，但其处理预取外，还会对资源进行解析，所以还要增加属性 as，说明资源的类型

```
<link rel="preload" href="URL" as="MIME_TYPE">

```

## 预取资源

`prefetch` 表示用户在接下来的浏览中（例如在下一个页面），有可能用到对应资源，提示浏览器要在闲时获取对应资源

先新建文件夹 prefetch-preload-demo（本文所有代码将在此创建），安装相关依赖，并新建文件夹 static

> mkdir prefetch-preload-demo
> cd prefetch-preload-demo
> npm init -y
> npm i -D http-server
> mkdir static

在 static 中创建 `prefetch.html`， `main.js` 和 `script.js`

`prefetch.html` 定义了一个 `rel` 为 `prefetch` 的链接

```
<html>
<head>
<title>Prefetch</title>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
<link rel="prefetch" href="script.js">
</head>
<body>
<script src="main.js"></script>
</body>
</html>

```

`main.js` 创建了一个按钮，并绑定了点击事件

```
let button = document.createElement('button');
button.innerHTML = 'Add Script';
button.addEventListener('click', e => {
  let script = document.createElement("script");
  script.src = "script.js";
  document.head.appendChild(script);
});
document.body.appendChild(button);

```

`script.js` 只是简单的打印了一下

```
console.log('script run');

```

运行服务器（也可在 `package.json` 中增加 `server` 脚本）

> npx http-server

访问 [http://localhost:8080](http://localhost:8080/) 并导航至 `static` 中，点击 prefetch.html，或者直接访问线上[页面](https://chanvinxiao.com/demo/html/prefetch.html)，初始状态下，查看控制台的网络选项卡下的内容如下（不要勾选 `Disable Cache`，点击右侧齿轮，勾选 `Use large request rows`）

![](https://upload-images.jianshu.io/upload_images/10024246-866a5d00121799d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


*   `script.js` 被 fetch 下来，size 列的两个数字，275 B 表示下载的字节大小，0 B 表示解析的字节大小（即目前并没有解析）
*   控制台是空的，即脚本没有运行

点击页面上的 `Add Script`，会在页面增加地址为 `script.js` 的 `<script>` 标签，此时网络选项卡会增加以下内容

![](https://upload-images.jianshu.io/upload_images/10024246-b41e6c0e3f87d223.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


*   下载字节量为 `(prefetch cache)` ，即直接从预取缓存获取资源，下面的解析后的字节不再为 0
*   控制台打印出脚本中的调试内容，即这时脚本才被解析并运行

## 预加载资源

`preload` 表示用户在当前的浏览中（往往是在当前页面），极有可以可能用到对应资源，提示浏览器要优先获取对应资源

将 prefetch.html 的 link 标签的 prefetch 改为 `preload`，并增加资源类型 `as` 为 `script`，即得 preload.html

```
<link rel="preload" href="script.js" as="script">

```

访问本地服务器对应的 `prefetch.html`，或者直接访问线上[页面](https://chanvinxiao.com/demo/html/preload.html)，初始状态下，查看控制台的网络选项卡下的内容如下

![](https://upload-images.jianshu.io/upload_images/10024246-4a5afe30da8888f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


*   `script.js` 被优先下载， size 列的解压字节不再为 0，即 `preload` 除了把脚本下载了下来，还进行了解析
*   控制台目前仍为空，即脚本虽然被解析，但并没有运行

点击 `Add Script`，网络选项卡并没有增加任何记录，但是控制台输出了脚本的打印内容

*   因为脚本已经解析完成，所以连从缓存获取都不需要了，直接运行即可
*   如果没有在 3 秒内点击 `Add Script`，控制台会进行警告，因为没有及时使用应该优先加载的资源

> The resource [https://chanvinxiao.com/demo/html/script.js](https://chanvinxiao.com/demo/html/script.js) was preloaded using link preload but not used within a few seconds from the window's load event. Please make sure it has an appropriate `as` value and it is preloaded intentionally.

## webpack 的相关处理

运行以下命令安装相关依赖，并新建文件夹 src

> npm i -D webpack webpack-cli html-webpack-plugin preload-webpack-plugin@3.0.0-beta.4
> mkdir src

*   PreloadWebpackPlugin 的当前版本 2.x 与 webpack 当前版本 4.x 不兼容，所以需要指定版本号为最新的 3.x beta

将 `main.js` 与 `script.js` 复制到 src 中，并将 `main.js` 的点击事件处理更新为

```
button.addEventListener('click', e => {
  import(/* webpackChunkName: "script" */ './script.js');
});

```

*   [import()](https://webpack.js.org/api/module-methods/#import-1) 为动态加载脚本，webpack 会生成类似以上动态创建 `script` 标签的代码
*   import 里的注释为特殊含义的魔法注释，如果不设置 webpackChunkName，加载的脚本将被按数字次序命名

增加 `webpack.config.js` 如下

```
const HtmlWebpackPlugin = require('html-webpack-plugin');
const PreloadWebpackPlugin = require('preload-webpack-plugin');

module.exports = {
  entry: './src/main.js',
  plugins: [
    new HtmlWebpackPlugin({
      filename: 'preload.html'
    }),
    new PreloadWebpackPlugin()
  ]
}

```

*   [HtmlWebpackPlugin](https://webpack.js.org/plugins/html-webpack-plugin/) 将自动生成相应的 html 文件，默认为 index.html，这里通过设置 `filename` 选项更改
*   [PreloadWebpackPlugin](https://github.com/GoogleChrome/preload-webpack-plugin) 为 HtmlWebpackPlugin 的插件，默认为其动态加载资源增加链接类型为 `preload` 的 `link` 标签，其 `as` 的值可根据后缀自动判断

PreloadWebpackPlugin 也支持 prefetch，需要增加 `rel` 选项为 `prefetch`

```
    new HtmlWebpackPlugin({
      filename: 'prefetch.html'
    }),
    new PreloadWebpackPlugin({
      rel: 'prefetch'
    })

```

不过要同时生成 preload.html 和 prefetch.html，需要在对应的 PreloadWebpackPlugin 中设置 `excludeHtmlNames` 排除对方，否则会同时产生 preload 和 prefetch 的 link 标签

```
    new HtmlWebpackPlugin({
      filename: 'preload.html'
    }),
    new HtmlWebpackPlugin({
      filename: 'prefetch.html'
    }),
    new PreloadWebpackPlugin({
      excludeHtmlNames: ['prefetch.html']
    }),
    new PreloadWebpackPlugin({
      rel: 'prefetch',
      excludeHtmlNames: ['preload.html']
    })

```

构建文件（也可在 `package.json` 中增加 `build` 脚本）

> npx webpack

`dist` 文件夹中将生成 prefetch.html 和 preload.html，访问本地服务器对应地址，即可得到与以上静态页面同样的效果

## 总结

此文使用静态页面和 webpack 打包两种方式演示了预取和预加载的实现 [](https://github.com/vinzid/prefetch-preload-demo)，主要技术点如下：

*   ELEMENT.appendChild 动态创建脚本
*   import() 动态加载脚本并设置魔法注释
*   html-webpack-plugin 及其插件的配置
