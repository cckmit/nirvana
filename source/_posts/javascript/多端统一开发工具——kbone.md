---
title: 多端统一开发工具——kbone
date: 2022-04-20 16:28:03
categories: 前端
---
[kbone官方文档](https://developers.weixin.qq.com/community/minihome/mixflow/1213301129006825473)
[kbone专题课程](https://wechat-miniprogram.github.io/kbone/docs/#%E4%BB%8B%E7%BB%8D)

## kbone 是什么？
kbone 是一个致力于微信小程序和 Web 端同构的解决方案。
该框架于2020-02-26开源，于2020-03-26公测。
微信开放社区小程序是使用Kbone官方框架编写的小程序。

##### 简介
>微信小程序的底层模型和 Web 端不同，我们想直接把 Web 端的代码挪到小程序环境内执行是不可能的。kbone 的诞生就是为了解决这个问题，它实现了一个适配器，在适配层里模拟出了浏览器环境，让 Web 端的代码可以不做什么改动便可运行在小程序里。
简单来说，使用这个框架写一份代码，并进行一些配置，就可以在运行时渲染在web和小程序两端。

##### 优势
- 大部分流行的前端框架都能够在 kbone 上运行，比如 Vue、React、Preact 等。
- 支持更为完整的前端框架特性，因为 kbone 不会对框架底层进行删改（比如 Vue 中的 v-html 指令、Vue-router 插件）。
- 提供了常用的 dom/bom 接口，让用户代码无需做太大改动便可从 Web 端迁移到小程序端。
- 在小程序端运行时，仍然可以使用小程序本身的特性（比如像 live-player 内置组件、分包功能）。
- 提供了一些 Dom 扩展接口，让一些无法完美兼容到小程序端的接口也有替代使用方案（比如 getComputedStyle 接口）。
- Webpack与Kbone是强耦合的，开发需借助Webpack提供的基本依赖
## 快速上手

##### 使用 kbone-cli 快速开发

对于新项目，可以使用 kbone-cli 来创建项目，首先安装 kbone-cli:

```
npm install -g kbone-cli

```

创建项目：

```
kbone init my-app

```

进入项目，按照 README.md 的指引进行开发：

```
// 开发小程序端
npm run mp
// 开发 Web 端
npm run web
// 构建 Web 端
npm run build

```

### 使用模板快速开发

除了使用 kbone-cli 外，也可以直接将现有模板 clone 下来，然后在模板基础上进行开发改造：

*   [Vue 项目模板](https://github.com/wechat-miniprogram/kbone-template-vue)
*   [React 项目模板](https://github.com/wechat-miniprogram/kbone-template-react)
*   [Preact 项目模板](https://github.com/wechat-miniprogram/kbone-template-preact)
*   [Omi 项目模板](https://github.com/omijs/template-kbone)

项目 clone 下来后，按照项目中 README.md 的指引进行开发。
### 进阶使用

刚刚是用脚手架做了一个简单的运行和效果的展示，事实上，我们在项目中更多的需要是更多复杂页面的交互，或者借助 Kbone 快速实现 Web 项目到微信小程序项目的转换。那么应该怎么做呢？接下来我们先简单了解一下它的原理，之后再做一个多页开发的尝试。

#### 原理

我们知道，小程序是双线程的，并没有Dom树的概念，在小程序中渲染和逻辑则完全分离，逻辑层是一个纯粹的JSCore，开发者可以编写 js 脚本，但是无法直接调用 dom/bom api，没有任何浏览器相关的实现，渲染和逻辑的交互通过数据和事件来驱动，开发者可以不用在去关心渲染的细节。
![](https://upload-images.jianshu.io/upload_images/10024246-3e6102107480fc58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

目前业界流行的第三方跨端框架们，常规做法都是静态编译兼容，原理是把代码语法分析一遍，然后将其中的模板部分翻译成对应的跨端需求的模板（微信小程序、支付宝小程序、H5、APP等）。
静态编译最大的局限性是无法保证转换的完整性，因为Vue模板和WXML模板的语法并不是直接对等的，Vue的特性设计也和小程序的设计无法划等号，这自然就导致了部分Vue特性的丢失。比如像Vue中的v-html指令、ref获取Dom节点、过滤器等就通通用不了。除了Vue自身的特性外，一些原本依赖Dom/Bom接口的Vue插件页无法使用，例如Vue-Router。对比一下，就会发现，kbone适配器的方式的优点就很容易显现出来，它不会对 vue runtime 进行裁剪魔改，比如 v-html、ref、vue-router 等都可以直接用（后面会比较分析）

与其他同构框架不同，kbone 是以适配器的方式来支持的。
![](https://upload-images.jianshu.io/upload_images/10024246-e8c647644dd210ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

适配器包含两部分：

> 负责提供 dom/bom api 的 js 库和负责渲染的自定义组件，也就是 kbone 中的 miniprogram-render 和 miniprogram-element，可以看到 kbone 最终生成的小程序代码里会依赖这两个 npm 包。
> 除此之外还需要一个 webpack 插件来根据原始的 Web 端源码生成小程序代码，因为小程序代码包和 Web 端的代码不同，它有固定的结构，而这个插件就是 mp-webpack-plugin。

简单总结一下，就是下面这个图：
![](https://upload-images.jianshu.io/upload_images/10024246-7a89bfaec7cf18a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 目录

通过[官方](https://link.segmentfault.com/?enc=X20W6kH4HVeq1bna85hrEw%3D%3D.2duj2J0xmGnIbjV2a%2FouFmcw3uZUBZgVP6d6rHOkrlOBCOMKlfBa1FxlzJTcoLmOK0rJ4nfnrN6NRpQxB7iXRw%3D%3D)给我们的这个目录结构，我们可以很清晰的看到每个目录下各个文件的作用。这里我就对其中的一些文件进行解释一下。

```
├─ build //配置目录
│  ├─ miniprogram.config.js  // mp-webpack-plugin 配置
│  ├─ webpack.base.config.js // Web 端构建基础配置
│  ├─ webpack.dev.config.js  // Web 端构建开发环境配置
│  ├─ webpack.mp.config.js   // 小程序端构建配置
│  └─ webpack.prod.config.js // Web 端构建生产环境配置
├─ dist //目标代码目录
│  ├─ mp                     // 小程序端目标代码目录，使用微信开发者工具打开，用于生产环境
│  └─ web                    // web 端编译出的文件，用于生产环境
├─ src //源码目录
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
└─ index.html                // Web 端入口模板（输出页面）
```

##### miniprogram.config.js

这个文件是关于小程序端的一些配置，类似于原生的 `json` 配置

##### webpack.mp.config.js

小程序端构建配置，也就是构建小程序端代码的 `webpack` 配置，多页开发中会用到其中的一部分配置。

##### src/mp & main.mp.js

`mp` 用来存放小程序端的入口文件，这里设置小程序的一些页面，`main.mp.js` 相当于一个挂载操作，把它看成 `mpvue` 里面的 `main.js` 比较好理解，设置页面路由和挂载映射 Vue 里面的页面。

#### demo13

##### 项目结构
![](https://upload-images.jianshu.io/upload_images/10024246-a9736aefd99cbfca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 入口
![](https://upload-images.jianshu.io/upload_images/10024246-d6b30ce3fc5e8ba9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-30a0faa1f41b6c89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 配置

#### 多页开发 -pro

##### Vue 的路由配置

src/router/index.js

##### 页面建立

src/各页面

##### 小程序端页面建立/挂载

src/mp main.mp.js

##### 小程序入口

##### miniprogram.config.js(按需配置)

##### web 自定义组件（tabbar）



