---
title: webpack之插件optimize css assets webpack plugin
date: 2021-03-20 17:44:26
categories: webpack
---
## 1. 安装：
主要是用来压缩css文件
```
npm i -D optimize-css-assets-webpack-plugin
// webpack 3及以下
npm i -D optimize-css-assets-webpack-plugin@3.2.0 
```
## 2. webpack.config.js配置
```
var OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        loader: MiniCssExtractPlugin.extract('style-loader', 'css-loader')
        loader: MiniCssExtractPlugin.loader,
        options: {publicPath: '/public/path/to/' }
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin('styles.css'),
    //new OptimizeCssAssetsPlugin()
    new OptimizeCSSAssetsPlugin({
      assetNameRegExp: /\.css$/g,
      cssProcessor: require('cssnano'),
      cssProcessorPluginOptions: {
        preset: ['default', { discardComments: { removeAll: true } }],
      },
      canPrint: true
    }),
  ]
};
```
## 3. 参数
assetNameRegExp：一个正则表达式，指示应优化\最小化的资产的名称。提供的正则表达式针对配置中
ExtractTextPlugin实例导出的文件的文件名运行，而不是源CSS文件的文件名。默认为/.css$/g
cssProcessor：用于优化\最小化CSS的CSS处理器，默认为cssnano。这应该是一个跟随
cssnano.process接口的函数（接收CSS和选项参数并返回一个Promise）。
cssProcessorOptions：传递给cssProcessor的选项，默认为 {}
cssProcessorPluginOptions：传递给cssProcessor的插件选项，默认为 {}
canPrint：一个布尔值，指示插件是否可以将消息打印到控制台，默认为 true
>webpack4.X版本中，webpack -p命令，是可以默认压缩js文件的
  