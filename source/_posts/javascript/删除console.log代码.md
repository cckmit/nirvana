---
title: 删除console.log代码
date: 2021-10-14 16:28:03
categories: 前端
---
说起`console.log`调试，不用多说，那是非常的好用，开发中帮助我们解决了不少`Bug`。我们经常能在**开发环境**中看见这一坨一坨的`console`调试。但是生产环境是绝不对不允许出现`console`信息代码的。你还在手动一个一个删除吗，那得多累啊！

下面我们来看一下这几种方式清除**生产环境**`console`无用代码。

## 基本操作

### Webpack配置

#### uglifyjs-webpack-plugin

![](https://upload-images.jianshu.io/upload_images/10024246-ff7ae2fd6d522da2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们可以看一下该插件介绍，该插件是用于减少我们代码`js`代码体积。并且如果安装运行该插件的话，`node`版本是在`v6.9.0+`和`Webpack`版本`v4.0.0+`。

官网地址看这里：[uglifyjs-webpack-plugin](https://www.npmjs.com/package/uglifyjs-webpack-plugin)

**安装**

```
npm i uglifyjs-webpack-plugin
```

**使用**

在`webpack.config.js`文件下进行如下配置。

```
const UglifyJsPlugin = require('uglifyjs-webpack-plugin')
module.exports = {
    // 省略...
    mode: "production",
    optimization: {
        minimizer: [
          new UglifyJsPlugin({
            uglifyOptions: {
              // 删除注释
              output:{
                comments: false
              },
              compress: {
                drop_console: true, // 删除所有调式带有console的
                drop_debugger: true,
                pure_funcs: ['console.log'] // 删除console.log
              }
            }
          })
        ]
      } 
}
```

配置完上面代码，重启即可看到效果。**注意：代码只会在production（生产环境）环境下有效**，看上面我们的配置`mode: production`，就是生产环境。来讲解一下上面这俩个属性`drop_console`和`pure_funcs`的区别，前者则是删除所有带console的前缀的调试方法，如：`console.log`、`console.table`、`console.dir`只要带有`console`前缀则全部删除。而后者则是配置，就是数组的值是什么它才会删除什么，比如`pure_funcs：[console.log, console.dir]`那么只会删除这两项，则不会删除代码中的`console.table`代码。

> 以上代码放到生产环境下，console调试代码即可清除，但是还有一个问题需要注意，就是该插件只支持`ES5`语法，如果你的代码中涉及到`ES6`语法则会报错。

#### terser-webpack-plugin

![](https://upload-images.jianshu.io/upload_images/10024246-7204c70bb25cc46a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该插件跟上面`uglifyjs-webpack-plugin`一样，都是用于减少我们代码`js`代码体积。

看上面描述：如果你的`Webpack`版本大于5+，则不需要安装此`terser-webpack-plugin`插件，会自带`terser-webpack-plugin`。但你的`Webpack`版本还是4，则你需要安装`terser-webpack-plugin`4的版本

**安装**

```
npm i terser-webpack-plugin@4
```
**使用**

```
const TerserWebpackPlugin = require("terser-webpack-plugin");

module.exports = {
    // 省略...
    mode: "production",
    optimization: {
        minimizer: [
              new TerserWebpackPlugin({
                terserOptions: {
                  compress: {
                    warnings: true,
                    drop_console: true,
                    drop_debugger: true,
                    pure_funcs: ['console.log', "console.table"] // 删除console
                  }
                }
            });
        ]
    }
}
```

该插件功能与上面一样，属性用法也一样，唯一该插件可支持`ES6`语法。都是在**生产环境**代码生效。

### Vue-cli配置

这是在`Vue-cli`项目中推荐使用的清除console插件。更多介绍看这里 [babel-plugin-transform-remove-console](https://www.npmjs.com/package/babel-plugin-transform-remove-console)

**安装**

```
npm i babel-plugin-transform-remove-console --save-dev
```

**使用**

在项目根目录`babel.config.js`文件下配置。该插件不区分**生产环境**或者**开发环境**，只要你配置都能生效。

```
module.exports = {
    plugins: [
        "transform-remove-console"
    ]
}

// 生产环境如下配置

const prodPlugins = []
if (process.env.NODE_ENV === 'production') {
    prodPlugins.push('transform-remove-console')
}
module.exports = {
    plugins: [
        ...prodPlugins
    ]
}
```

### 简单粗暴删除

直接重写`console.log`的方法。不推荐该写法，这种写法代码里面的console.log方法仍然会执行，只不过没有输出

```
console.log = function () {};
```

### 手写Loader删除console

我们来写一个简易版的清除console插件。

新建一个`js`文件，我这里名为`clearConsole.js`，其实这里也是用正则去匹配然后替换为空。

**clearConsole.js**

```
const reg = /(console.log\()(.*)(\))/g;
module.exports = function(source) {
    source = source.replace(reg, "")
    return source;
}
```
在`Vue.config.js`配置

```
module.exports = {
    // 省略...
    configureWebpack: {
        module: {
            rules: [
                {
                    test: /\.vue$/,
                    exclude: /node_modules/,
                    loader: path.resolve(__dirname, "./clearConsole.js")
                },
                {
                    test: /\.js$/,
                    exclude: /node_modules/,
                    loader: path.resolve(__dirname, "./clearConsole.js")
                }
            ],
        }
    },
}
```

配置如上代码就可以啦~，清除`js`文件和`vue`文件里的`console.log`。`exclude`代表不去`node_module`目录下查找。
