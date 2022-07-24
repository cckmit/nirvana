---
title: 微信小程序跨平台框架-KBONE 浅析
date: 2022-04-30 16:28:03
categories: 前端
tags: [小程序]
---

![](https://upload-images.jianshu.io/upload_images/10024246-73b34e3b7692e983.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## KBONE 是什么
>Kbone 是一个致力于微信小程序和 Web 端同构的解决方案，在适配层里模拟出浏览器环境，让 Web 端的代码可以不做什么改动便可运行在小程序里。

Kbone 这个框架可以让你只需要写一份代码，就能够在两端运行，只需要进行一些配置，轻松跑小程序和 Web 两个端。
##### Kbone 特点
由于小程序与传统的WEB端运行时存在差异，为了实现多端同构需要对源代码进行一些处理，目前市面上有多个跨平台的小程序框架
kbone 区别于市面上的大部分跨平台框架存在以下特点：
- 不同于市面上的大部分跨平台框架采用编译处理的方式，kbone 是运行时通过适配层做小程序页面的渲染
- 不强依赖特定的DSL，支持 vue、react、原生js 等多种web端技术栈
- 具有更为全面的 web 特性支持
- kbone 只支持 微信小程序和 web 端
与其他多端框架对比：
![](https://upload-images.jianshu.io/upload_images/10024246-f714af50837adf9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### kbone使用场景
Kbone 可以让开发者用前端的主流技术栈来开发微信小程序，支持完整的框架特性，提供了常用的 DOM、BOM 接口，让业务代码无需太大改动就能直接迁移到微信小程序端。并且保留了原生小程序本身的特性及原生小程序的内置组件
- 需要从 web 版本的源代码迁移到小程序
- 对性能要求不苛刻
- 页面节点数量不是很大
- 只有微信小程序 和 H5 端需求
## Kbone 初探
如果要使用 kbone 开发跨平台应用，需要遵守以下三点条件：
- 强耦合 webpack，应用必须使用 webpack 进行构建
- 页面所有的 html 标签均由 js 调用 DOM API 创建
- 入口文件需要导出 createApp 方法
##### Kbone 目录了解
```
├─ build
│  ├─ miniprogram.config.js  // mp-webpack-plugin 配置
│  ├─ webpack.base.config.js // Web 端构建基础配置
│  ├─ webpack.dev.config.js  // Web 端构建开发环境配置
│  ├─ webpack.mp.config.js   // 小程序端构建配置
│  └─ webpack.prod.config.js // Web 端构建生产环境配置
├─ dist
│  ├─ mp                     // 小程序端目标代码目录，使用微信开发者工具打开，用于生产环境
│  └─ web                    // web 端编译出的文件，用于生产环境
├─ src
│  ├─ common                 // 通用组件
│  ├─ mp                     // 小程序端入口目录
│  │  ├─ home                // 小程序端 home 页面
│  │  │  └─ main.mp.js       // 小程序端入口文件
│  │  └─ other               // 小程序端 other 页面
│  │     └─ main.mp.js       // 小程序端入口文件
│  ├─ detail                 // detail 页面
│  ├─ home                   // home 页面
│  ├─ list                   // list 页面
│  ├─ router                 // vue-router 路由定义
│  ├─ store                  // vuex 相关目录
│  ├─ App.vue                // Web 端入口主视图
│  └─ main.js                // Web 端入口文件
└─ index.html                // Web 端入口模板
```
##### 项目运行
小程序端：`npm run mp`
Web端:    ` npm run web`
##### Web 端  与 mp webpack 配置对比
![](https://upload-images.jianshu.io/upload_images/10024246-db1ade36559963cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## KBONE 为什么可以跨平台
- 微信小程序是双线程模型，逻辑层 和视图层是完全分离的，逻辑层 无法直接操作 视图层，两个线程之间的通信需要通过 setData 和 bind 事件监听
- web 端框架 逻辑层 和 视图层在一同一个线程之中，逻辑层可以直接操作DOM。现有前端框架的做法是在编译阶段将模板转成 js 函数，然后在运行时调用这个 js 方法
- 编译期处理的框架的做法，在项目构建阶段通过语法树转换的方式将 web 端的代码转成 小程序端的代码。由于编译器对语法树的转换是固定的所以只能针对指定的语法进行转换
- Kbone 采用的是运行时处理代码，这就要求 kbone 需要针对更加底层的 DOM、BOM 做适配
![](https://upload-images.jianshu.io/upload_images/10024246-5e305237d82ce0ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Kbone 的适配器核心包含两个部分：
- `miniprogram-element：`通过小程序自定义组件对原生的小程序进行封装，主要通过小程序的自定义组件处理小程序视图层的渲染，同时监听用户行为，模拟 web 的事件机制
- `miniprogram-render： `仿造Dom/Bom接口，构造仿造Dom树
![](https://upload-images.jianshu.io/upload_images/10024246-bc1e22e31a80bf31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### miniprogram-element 介绍
```
{
    "enablePullDownRefresh": false,
    "usingComponents": {
        "element": "miniprogram-element"
    }
}
```
```
<element
    wx:if="{{pageId}}"
    class="{{bodyClass}}"
    style="{{bodyStyle}}"
    data-private-node-id="e-body"
    data-private-page-id="{{pageId}}"
></element>
```
例如封装了一个 custom-dom 组件，这个组件里面也使用了 custom-dom 组件用于渲染子组件。那么只要我们执行一下 setData，把 children 数据传递过去就可以创建出子组件，子组件本身也是 custom-dom 组件，它同样可以执行这个逻辑把各自的子组件创建出来，这样就实现了组件的递归创建，只要我们拥有完整的 Dom 树结构，就可以创建出相对应的一棵组件树。
![](https://upload-images.jianshu.io/upload_images/10024246-42e85e92ea8eb4a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
逻辑层 会将需要渲染的小程序 webview 元素生成对应的虚拟 DOM 树，然后通过 setData 方法将生成好的虚拟 DOM 树 以字符串的形式传递到视图层，但是生成的 虚拟 DOM 树 存在很大的不确定性，无法用固定的 WXML 与之对应，因此需要一种方式将这种不确定的虚拟 DOM 树正确地渲染出来。
Kbone 将接收到的 虚拟DOM 树数据以自定义组件配合自定义组件的递归调用的方式，将虚拟 DOM 树渲染成对应的 WXML 结构。
![](https://upload-images.jianshu.io/upload_images/10024246-a62d90eb8cafbc4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### miniprogram-render介绍
![](https://upload-images.jianshu.io/upload_images/10024246-c68f154d8f2390e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### mp-webpack-plugin
`mp-webpack-plugin` 是 webpack 的插件，主要的功能是根据 webpack 的入口配置进行依赖计算，生成小程序文件模板、生成小程序的全局、页面配置、处理页面路由。
在使用` mp-webpack-plugin `时 webpack 的 配置需要做相关的变更
```
entry: {
    home: path.resolve(__dirname, '../src/mp/home/main.mp.js'),
    other: path.resolve(__dirname, '../src/mp/other/main.mp.js'),
  },
  output: {
    path: path.resolve(__dirname, '../dist/mp/common'), // 放到小程序代码目录中的 common 目录下
    filename: '[name].js', // 必需字段，不能修改
    library: 'createApp', // 必需字段，不能修改
    libraryExport: 'default', // 必需字段，不能修改
    libraryTarget: 'window' // 必需字段，不能修改
  }
```
## KBONE 要注意什么
##### 小程序页面和组件的生命周期
Kbone 通过事件的方式来处理小程序中的页面或者组件生命周期hook
- window: wxload 事件
即小程序页面的 onLoad 生命周期回调。
- window: wxshow 事件
即小程序页面的 onShow 生命周期回调。
- window: wxready 事件
即小程序页面的 onReady 生命周期回调。
- window: wxhide 事件
即小程序页面的 onHide 生命周期回调。
- window: wxunload 事件
即小程序页面的 onUnload 生命周期回调
##### 小程序分包
分包配置需要在 mp-webpack-plugin 的配置文件中设置
```
// mp-webpack-plugin 配置
{
    generate: {
        subpackages: {
            package1: ['list'],
            package2: ['detail'],
        },
    },
    // 其他配置...
}
```
##### 使用小程序内置组件
部分内置组件在 html 中没有标签可替代，那就需要使用 wx-component 标签或者使用 wx- 前缀，基本用法如下：
```
<!-- wx-component 标签用法 -->
<wx-component behavior="picker" mode="region" @change="onChange">选择城市</wx-component>
<wx-component behavior="button" open-type="share" @click="onClickShare">分享</wx-component>
<!-- wx- 前缀用法 -->
<wx-picker mode="region" @change="onChange">选择城市</wx-picker>
<wx-button open-type="share" @click="onClickShare">分享</wx-button>
```
要在 kbone 中使用自定义组件，需要将所有自定义组件和其依赖放到一个固定的目录，这个目录可以自己拟定，假设这个目录为 src/custom-components：
- 在 mp-webpack-plugin 插件配置中指定自定义组件目录
- 将自定义组件放入组件根目录
- 使用自定义组件
```
module.exports = {
    generate: {
        wxCustomComponent: {
            root: path.join(__dirname, '../src/custom-components'),
            usingComponents: {
                'comp-a': 'comp-a/index',
            }
        }
    },
    // ... other options
}
```
##### 状态管理
在 kbone 中，每个页面拥有独立的 window 对象，页面与页面间是相互隔离的，为此需要一个跨页面通信和跨页面数据共享的方式，kbone 提供了 `window.$$subscribe`、`window.$$publish` 的方式进行页面通信。这种方式允许多页形式进行通讯，我们可以在此基础上封装多页应用的 状态管理 组件。
##### 节点数目
如果你的页面节点数量特别多（通常在 1000 节点以上），同时还要保证在节点数无限上涨时仍然有稳定的渲染性能的话，可以尝试一下业内采用静态模板转译的方案；
>更多要注意的问题可以参考：[限制](https://wechat-miniprogram.github.io/kbone/docs/qa/#%E9%99%90%E5%88%B6)