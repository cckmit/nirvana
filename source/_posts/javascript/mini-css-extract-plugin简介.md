---
title: mini-css-extract-plugin简介
date: 2021-03-11 21:12:40
categories: 前端
---

将css单独打包成一个文件的插件，它为每个包含css的js文件都创建一个css文件。它支持css和sourceMaps的按需加载。

目前只有在webpack V4版本才支持使用该插件

和extract-text-webpack-plugin相比：

- 异步加载
- 无重复编译，性能有所提升
- 用法简单
- 支持css分割
例子：

##### 支持HMR
* Install *
```
npm install --save-dev mini-css-extract-plugin
 ```
##### 用法
- 简单的例子
webpack.config.js
```
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports={
    plugins:[
        new MiniCssExtractPlugin({
        // Options similar to the same options in webpackOptions.output
        // both options are optional
        filename: "[name].css",
        chunkFilename: "[id].css"
        })
    ],
     module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              // you can specify a publicPath here
              // by default it use publicPath in webpackOptions.output
              publicPath: '../'
            }
          },
          "css-loader"
        ]
      }
    ]
  }
}
```
- 高级的配置

这个插件应该只在生产环境构建中使用，并且在loader链中不应该有style-loader，特别是我们在开发模式中使用HMR时。

下面是一个在开发模式中使用HMR并在生产环境构建的文件中提出出css样式的示例。

(各个loader的配置根据你的需求进行自定义修改)

webpack.config.js
```
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const devMode = process.env.NODE_ENV !== 'production'

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: devMode ? '[name].css' : '[name].[hash].css',
      chunkFilename: devMode ? '[id].css' : '[id].[hash].css',
    })
  ],
  module: {
    rules: [
      {
        test: /\.(sa|sc|c)ss$/,
        use: [
          devMode ? 'style-loader' : MiniCssExtractPlugin.loader,
          'css-loader',
          'postcss-loader',
          'sass-loader',
        ],
      }
    ]
  }
}
```
生产环境最小化
等到webpack V5时，有可能会将css压缩功能进行集成，但是在webpack V4，我们还需要手动使用optimize-css-assets-webpack-plugin来进行css的压缩。配置wepack的optimization.minimizer选项来覆盖webpack的默认配置。同时也要指定一个js压缩器。

wepack.config.js
```
const UglifyJsPlugin = require("uglifyjs-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");

module.exports={
  optimization: {
    minimizer: [
      new UglifyJsPlugin({
        cache: true,
        parallel: true,
        sourceMap: true // set to true if you want JS source maps
      }),
      new OptimizeCSSAssetsPlugin({})
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          "css-loader"
        ]
      }
    ]
  }
}
}
```
- 其他功能

使用预加载或内联CSS
运行时代码通过link 或 style标签检测已经添加的css,当在服务端注入css为了在服务端进行渲染时是非常有用的。link标签的href属性用来匹配被加载的css块的URL，data-href属性也可以在link和style标签上使用，当使用内联样式的时候必须使用href.

提取所有的css到一个文件中
类似于 extract-text-webpack-plugin的作用，可以使用optimization.splitChunks.cacheGroups来将css提取到一个文件中

webpack.config.js
```
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  optimization: {
    splitChunks: {
      cacheGroups: {
        styles: {
          name: 'styles',
          test: /\.css$/,
          chunks: 'all',
          enforce: true
        }
      }
    }
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css",
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          "css-loader"
        ]
      }
    ]
  }
}
```
- 根据webpack 的 entry 提取 css

还可以根据webpack entry提取CSS。如果动态导入路由，但希望根据条目将CSS绑定在一起，那么这一点尤其有用。这也防止了使用ExtractTextPlugin时出现的CSS复制问题。

webpack.config.js
```
const path = require('path');
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

function recursiveIssuer(m) {
  if (m.issuer) {
    return recursiveIssuer(m.issuer);
  } else if (m.name) {
    return m.name;
  } else {
    return false;
  }
}
module.exports = {
  entry: {
    foo: path.resolve(__dirname, 'src/foo'),
    bar: path.resolve(__dirname, 'src/bar')
  },  
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css",
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          "css-loader"
        ]
      }
    ]
  },
  optimization:{
    splitChunks:{
      cacheGroups:{
          fooStyles:{
            fooStyles: {
            name: 'foo',
            test: (m,c,entry = 'foo') => m.constructor.name === 'CssModule' && recursiveIssuer(m) === entry,
            chunks: 'all',
            enforce: true
            }
          }
      }
    }  
  }
}
```
