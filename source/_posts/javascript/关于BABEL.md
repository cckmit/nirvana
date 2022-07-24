---
title: 关于BABEL
date: 2021-04-21 14:47:39
categories: 前端
---
## 什么是 BABEL

> Babel 官方文档: https://babeljs.io/

我们知道各个浏览器对 JavaScript 版本的支持各不相同，有很多优秀的新语法都不能直接在浏览器中运行。为了解决这个“沟通不畅”的问题，所以就有了 Babel，Babel 的出现使得我们可以无须顾忌的去使用 ES6+ 的语法。

> Babel is a JavaScript compiler.

这也是为何我们必须使用 ES6+ 语法的前提条件。

## BABEL 如何编译

先看下面这张图：![](https://upload-images.jianshu.io/upload_images/10024246-d39f2e8ae58b73a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你会发现 ES6 的语法确实被编译成浏览器可以识别的版本了，你是不是也在问这是怎么做到的呢？

### BABEL 编译的阶段

> babel 总共分为三个阶段：解析，转换，生成。

我们需要知道现在 babel 本身是不具备这种转化功能，提供这些转化功能的是一个个 plugin。所以我们没有配置任何 plugin 的时候，经过 Babel 输出的代码是没有改变的。

![640?wx_fmt=png](https://upload-images.jianshu.io/upload_images/10024246-5e8ec00624dcbc19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### PLUGIN —— TRANSFORM 的载体

> Babel 自 6.0 起，就不再对代码进行转换。现在只负责图中的 parse 和 generate 流程，转换代码的 transform 过程全都交给插件去做。

例子：

```
// 模板字面量	
const name = '小生方勤';	
let hello = `hello ${name}`;
```

上面是一个简单的模板字面量的例子，我们清楚这个是 ES6 的新特性，在不支持 ES6 的运行平台这段代码是会报错的，所以我们需要 Babel 来将其编译成 ES5 的代码。

所以我们需要如下来配置 babel：

```
// .babelrc 文件	
{ 	
  "plugins": [	
    "transform-es2015-template-literals"  // 转译模版字符串的 plugins	
  ],	
  "presets": ["env", "stage-2"]	
}
```

**preset（即一组预先设定的插件）**

> preset: babel 插件集合的预设，包含某些插件 plugin。显然像上面那样一个一个配置插件会非常的麻烦，为了方便，babel 为我们提供了一个配置项叫做 persets（预设）。

当前 babel 推荐使用 babel-preset-env 替代 babel-preset-es201X ,env 的支持范围更广，包含es201X 的所有语法编译，并且它可以根据项目运行平台的支持情况自行选择编译版本。

#### PLUGINS 与 PRESETS 同时存在的执行顺序

1.  先执行 plugins 的配置项,再执行 Preset 的配置项；

2.  plugins 配置项，按照声明顺序执行；

3.  Preset 配置项，按照声明逆序执行。

列入以下代码的执行顺序为：

1.  transform-es2015-template-literals

2.  stage-2

3.  env

```
// .babelrc 文件	
{ 	
    "plugins": [	
        "transform-es2015-template-literals",  // 转译模版字符串的 plugins	
    ],	
    "presets": [	
        ["env", {	
            // 是否自动引入 polyfill，开启此选项必须保证已经安装了 babel-polyfill	
            // “usage” | “entry” | false, defaults to false.	
            "useBuiltIns": "usage"	
        }], "stage-2"]	
}
```

**这里讲一讲 useBuiltIns 配置**

我们可能在全局引入 babel-polyfill，这样打包后的整个文件体积必然是会变大的。

但是通过设置 `"useBuiltIns":"usage"` 能够把 babel-polyfill 中你需要用到的部分提取出来，不需要的去除。

**useBuiltIns 参数说明：**

1.  false: 不对 polyfills 做任何操作

2.  entry: 根据 target 中浏览器版本的支持，将 polyfills 拆分引入，仅引入有浏览器不支持的 polyfill

3.  usage(新)：检测代码中 ES6/7/8 等的使用情况，仅仅加载代码中用到的 polyfills

## BABEL 相关模块简要说明

了解过 Babel 的同学，是否也觉得的模块有点多呢？我开始学习的时候就有这种感觉。其实每个模块是各司其职的。

![](https://upload-images.jianshu.io/upload_images/10024246-15ad7a0856c930aa.JPEG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### BABEL-CORE（核心）

这个模块是最能顾名思义的了，即 babel 的核心模块。babel 的核心 api 都在这个模块中。也就是这个模块会把我们写的 js 代码抽象成 AST 树；然后再将 plugins 转译好的内容解析为 js 代码。

具体怎么工作的这里就不详细说了，因为我也不知道。

### BABEL-CLI

> babel-cli 官方文档：https://babeljs.io/docs/en/babel-cli/

babel-cli 是一个通过命令行对 js 文件进行转换的工具。

当然我们一般不会使用到这个模块，因为一般我们都不会手动去做这个工作，这个工作基本都集成到模块化管理工具中去了，比如 webpack、Rollup 等。

简单使用（需要先安装 babel-cli）：

```
babel test.js -o compiled.js
```

### BABEL-NODE

babel-node 是 babel-cli 的一部分，所以它在安装 babel-cli 的时候也同时安装了。

它使 ES6+ 可以直接运行在 node 环境中。

### BABEL-POLYFILL(内部集成了 CORE-JS 和 REGENERATOR)

babel 对一些新的 API 是无法转换，比如 Generator、Set、Proxy、Promise 等全局对象，以及新增的一些方法：includes、Array.form 等。所以这个时候就需要一些工具来为浏览器做这个兼容。

> 官网的定义：babel-polyfill 是为了模拟一个完整的 ES6+ 环境，旨在用于应用程序而不是库/工具。

**babel-polyfill 主要有两个缺点：**

1.  使用 babel-polyfill 会导致打出来的包非常大，很多其实没有用到，对资源来说是一种浪费。

2.  babel-polyfill 可能会污染全局变量，给很多类的原型链上都作了修改，这就有不可控的因素存在。

因为上面两个问题，所以在 Babel7 中增加了 babel-preset-env，我们设置 `"useBuiltIns":"usage"`这个参数值就可以实现按需加载 babel-polyfill 啦。

### BABEL-RUNTIME & BABEL-PLUGIN-TRANSFORM-RUNTIME

> 在使用 Babel6 的时候， .babelrc 文件中会使用 babel-plugin-transform-runtime，而 package.json 中的 dependencies 同时包含了 babel-runtime，因为在使用 babel-plugin-transform-runtime 的时候必须把 babel-runtime 当做依赖。

.babelrc 配置：

```
{	
  "presets": [	
    ["env"]	
  ],	
  "plugins": [	
    ["transform-runtime", {	
      "helpers": false, // defaults to true	
      "polyfill": false, // defaults to true	
      "regenerator": true, // defaults to true	
      "moduleName": "babel-runtime" // defaults to "babel-runtime"	
    }]	
  ]	
}
```

我们在启用插件 babel-plugin-transform-runtime 后，Babel 就会使用 babel-runtime 下的工具函数，将一些浏览器不能支持的特性重写，然后在项目中使用。

> babel-runtime 内部也集成了 core-js、 regenerator、helpers 等

由于采用了沙盒机制，这种做法不会污染全局变量，也不会去修改内建类的原型，所以会有重复引用的问题。

现在最好的实践应该是在 babel-preset-env 设置 "useBuiltIns": "usage"，按需引入 polyfill。

### 三种方案对比

| 方案 | 优点 | 缺点 |
| --- | --- | --- |
| @babel/runtime & @babel/plugin-transform-runtime | 按需引入, 打包体积小 | 不能兼容实例方法 |
| @babel/polyfill | 完整模拟 ES2015+ 环境 | 打包体积过大, 污染全局对象和内置的对象原型 |
| @babel/preset-env | 按需引入, 可配置性高 | 小生不知 -_- |

## BABEL7 的一些变化

### PRESET 的变更：

> 淘汰 es201x，删除 stage-x，推荐 env

如果你还在使用 es201x，官方建议使用 env 进行替换。**淘汰并不是删除，只是不推荐使用**。

但 stage-x 是直接被删了，也就是说在 babel7 中使用 es201X 是会报错的。

### 包名称变化

babel 7 的一个重大变化，把所有 babel-* 重命名为 @babel/*，

例如：

1.  babel-cli —> @babel/cli。

2.  babel-preset-env —> @babel/preset-env

### 低版本 NODE 不再支持

babel 7.0 开始不再支持 nodejs 0.10, 0.12, 4, 5 这四个版本，相当于要求 nodejs >= 6。

还有一些包从其他包独立出来的变化等等
