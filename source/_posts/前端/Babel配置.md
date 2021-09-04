---
title: Babel配置
date: 2021-06-10 16:28:03
category: javascript
---
## 1.babel

> babel是个什么玩意？ Babel本质上是一个编辑器，也就是个“翻译官”的角色，比如树酱听不懂西班牙语，需要别人帮我翻译成为中文，我才晓得。那么Babel就是帮助浏览器翻译的，让web应用能够运行旧版本的浏览器中，比如IE11浏览器不支持Promise等ES6语法，那这个时候在IE11打开你写的web应用，应用就无法正常运行，这时候就需要Babel来“翻译”成为IE11能读懂的

### 1.1 Babel是怎么工作的？

> 本质上单独靠Babel是无法完成“翻译”，比如官网的例子`const babel = code => code;`不借助Babel插件的前提，输出是不会把箭头函数“翻译”的，如果想完成就需要用到插件，更多概念点点击 [官方文档](https://www.babeljs.cn/docs)

Babel工作原理本质上就是三个步骤：解析、转换、输出，如下👇所示，

![](https://upload-images.jianshu.io/upload_images/10024246-37a114b8e6fddb2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 1.2 AST 是什么玩意？

> 👨🎓 啊斌同学： 上面说到的抽象语法树AST又是什么玩意？

答：我们上文提到，Babel在解析是时候会通过将code转换为AST抽象语法树，本质上是代码语法结构的一种抽象表示，通过以树🌲形的结构形式表现出它的语法结构，抽象在于它的语言形态不会体现在原始代码code中

下面介绍下在前端项目开发中一些AST的应用场景:

*   `Vue模版解析`： 我们平时写的`.vue`文件通过`vue-template-compiler`解析，.vue文件处理为一个AST
*   `Babel的“翻译”` : 如将ES6转换为ES5过程中转为AST
*   `webpack的插件UglifyJS`： `uglifyjs-webpack-plugin`用来压缩资源，uglifyjs会遇到需要解析es6语法，这个过程中本质上也是借助`babel-loader`

你可以安装通过本地安装`babel-cli`做个验证，通过babel-cli编译js文件，玩玩“翻译”

🌲推荐阅读：

*   [阮一峰 - Babel 入门教程](http://www.ruanyifeng.com/blog/2016/01/babel.html?bsh_bid=1656398345)

### 1.3 开发自己的babel插件需要了解什么？

> 👨🎓 啊可同学： 树酱，我想自己使用AST开发一个babel插件需要使用到哪些东西呢？

答：我们上一节中提到babel不借助“外援”的话，自己是无法完成翻译，而一个完整的“翻译”的过程是需要走完解析、转换、输出才能完成整个闭环，而这其中的每个环节都需要借助babel以下这些API

*   `@babel/parser`： babel解析器将源代码code解析成 AST
*   `@babel/generator`: 将AST解码生成js代码 new Code
*   `@babel/traverse `: 用来遍历AST树，可以用来改造AST～，如替换或添加AST原始节点
*   `@babel/core`：包括了整个babel工作流

下面是一个简单“翻译”的demo～

![](https://upload-images.jianshu.io/upload_images/10024246-1c95d7a164122e99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 👦：啊宽同学：你不是说`@babel/parser`是也将源代码code解析成 AST吗？为啥`@babel/core`也是？

答：@babel/core包含的是整个babel工作流，在开发插件的过程中，如果每个API都单独去引入岂不是蒙蔽了来吧～于是就有了@babel/core插件，顾名思义就是核心插件，他将底层的插件进行封装（包含了parser、generator等），提高原有的插件开发效率，简化过程，好一个“🍟肯德基全家桶”

🌲推荐阅读：

*   [Babel 插件手册](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/zh-Hans/plugin-handbook.md)
*   [实现一个真正的babel插件](https://zhuanlan.zhihu.com/p/91948992)
*   [理解babel的基本原理和使用方法](https://www.cnblogs.com/jyybeam/p/13375179.html)

### 1.4 Babel插件相关

> 讲完Babel的基本使用，接下来聊聊插件，上文提到单独靠babel是“难成大器”的，需要插件的辅助才能实现霸业，那插件是怎么搞的呢？

通过第一节的学习我们知道完成第一步骤解析完AST后，接下来是进入转换，插件在这个阶段就起到关键作用了。

#### 1.4.1 插件的使用

> 告诉Babel该做什么之前，我们需要创建一个配置文件.babelrc或者babel.config.js文件

如果我想把`es2015`的语法转化为es5 及支持es2020的链式写法，我可以这样写

![](https://upload-images.jianshu.io/upload_images/10024246-28c64992ef35966f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


上图所示👆，我们可以看到我们配置两个东西 `present`和`plugin`

> 👨🎓 啊可同学：babel不是只需要plugin来帮忙翻译吗，这个present又是什么玩意？

答：presets是预设，举个例子：有一天树酱要去肯德基买鸡翅、薯条、可乐、汉堡。然后我发现有个套餐A包含了(薯条、可乐、汉堡)，那这个present就相当于套餐A，它包含了一些插件集合，一个大套餐，这样我就只需要一个套餐A+鸡翅就搞定了，不用配置很多插件。

就好比上面的es2015“套餐”，其实就是Babel团队将同属ES2015相关的很多个`plugins`集合到`babel-preset-es2015`一个preset中去

> 👧 啊琪同学：@babel/preset-env这个是什么？我看很多babel的配置都有

答：@babel/preset-env这个是一个present预设，换句话说就是“豪华大礼包”，包括一系列插件的集合，包含了我们常用的es2015,es2016, es2017等最新的语法转化插件，允许我们使用最新的js语法，比如 let，const，箭头函数等等，但不包括stage-x阶段的插件。换句话说，他包含了我们上文提到了`es2015`，是个“全家桶”了，而不仅是个套餐了。

#### 1.4.2 自定义 present

> 👦 啊斌同学：树酱，那我是不是可以自己搞一个预设present？

答: 可以的，但是你可以以 babel-preset-* 的命名规范来创建一个新项目，然后创建一个packjson并安装好定影的依赖和一个index.js 文件用于导出 .babelrc，最终发布到npm中，如下所示

![](https://upload-images.jianshu.io/upload_images/10024246-70a6ed974c09dcc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 1.4.3 关于 polyfill

> 比如我们在开发中使用，会使用到一些es6的新特征比如`Array.from`等，但不是所有的 JavaScript 环境都支持 Array.from，这个时候我们可以使用 Polyfill（代码填充，也可译作兼容性补丁）的“黑科技”，因为babel只转换新的js语法，如箭头函数等，但不转换新的API，比如Symbol、Promise等全局对象，这时候需要借助`@babel/polyfill`，把es的新特性都装进来，使用步骤如下

*   `npm 安装` : `npm install --save @babel/polyfill`
*   `文件顶部导入 polyfill`: `import @babel/polyfilll`

🙅♂️：缺点：全局引入整个 polyfill包，如promise会被全局引入，污染全局环境，所以不建议使用，那有没有更好的方式？可以直接使用@babel/preset-env并修改配置，因为`@babel/preset-env`包含了`@babel/polyfill`插件，看下一节

#### 1.4.4 如何通过修改@babel/preset-env配置优化

> 完成上面的配置，然后用Babel编译代码，我们会发现有时候打出的包体积很大，因为@babel/polyfill有些会被全局引用，那你要弄清楚@babel/preset-env的配置

`@babel/preset-env` 中与 `@babel/polyfill` 的相关参数有两个如下：

*   `targets`: 支持的目标浏览器的列表
*   `useBuiltIns`: 参数有 “entry”、”usage”、false 三个值。默认值是false，此参数决定了babel打包时如何处理@babel/polyfilll 语句

主要聊聊关于`useBuiltIns`的不同配置如下：

*   `entry`: 去掉目标浏览器已支持的polyfilll 模块，将浏览器不支持的都引入对应的polyfilll 模块。
*   `usage`: 打包时会自动根据实际代码的使用情况，结合 targets 引入代码里实际用到部分 polyfilll模块
*   `false`: 不会自动引入 polyfilll 模块，对polyfilll模块屏蔽

🌲建议：使用 useBuiltIns: usage来根据目标浏览器的支持情况，按需引入用到的 polyfill 文件，这样打包体积也不会过大

#### 1.4.5 webpack打包如何使用babel？

> 对于`@babel/core`、`@babel/preset-env` 、`@babel/polyfill`等这些插件，当我们在使用webpack进行打包的时候，如何让webpack知道按这些规则去编译js。这时就需要`babel-loader`了，它相当于一个中间桥梁，通过调用babel/core中的API来告知webpack要如何处理。

#### 1.4.6 开发工具库，涉及到babel使用怎么避免污染环境？

> 👦 啊斌同学：我开发了一个工具库，也使用了babel，如果引用polyfill,如何避免使用导致的污染环境？

答：在开发工具库或者组件库时，就不能再使用babel-polyfill了，否则可能会造成全局污染，可以使用`@babel/runtime`。它不会污染你的原有的方法。遇到需要转换的方法它会另起一个名字，否则会直接影响使用库的业务代码，使用`@babel/runtime`主要在于

*   可以减小库和工具包的体积，规避babel编译的工具函数在每个模块里都重复出现的情况
*   在没有使用 `@babel/runtime` 之前，库和工具包一般不会直接引入 polyfill。否则像 Promise 这样的全局对象会污染全局命名空间，这就要求库的使用者自己提供 polyfill。这些 polyfill 一般在库和工具的使用说明中会提到，比如很多库都会有要求提供 es5 的 polyfill。在使用 babel-runtime 后，库和工具只要在 package.json 中增加依赖 babel-runtime，交给 babel-runtime 去引入 polyfill就可以了

如何使用 @babel/runtime

*   1.npm安装

```
npm install --save-dev @babel/plugin-transform-runtime
npm install --save @babel/runtime
```

*   2.配置
    ![](https://upload-images.jianshu.io/upload_images/10024246-79085fb26435043c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.5 关于babel容易混淆的点

#### 1.5.1 babel-core和@babel/core 区别

> 👦：啊呆同学：babel-core和@babel/core是什么区别？

答；@babel是在babel7中版本提出来的，就类似于 vue-cli 升级后使用@vue/cli一样的道理，所以babel7以后的版本都是使用 `@babel` 开头声明作用域，
