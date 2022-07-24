---
title: webpack配置autoprefixer
date: 2021-03-11 21:12:40
categories: webpack
---

Autoprefixer解析CSS文件并且添加浏览器前缀到CSS规则里，使用[Can I Use](http://www.caniuse.com/)的数据来决定哪些前缀是需要的。


1.安装autoprefixer：
```
cnpm install autoprefixer --save-dev
```
2.webpack.config.js中配置autoprefixer：

(1).引入autoprefixer：
```
const autoprefixer = require('autoprefixer');
```
(2).添加postcss-loader和插件配置：
```
          {
            loader: 'postcss-loader',
            options: {
              ident: 'postcss',
              plugins: [
                autoprefixer({
                     browsers: [
                          'last 10 Chrome versions',
                          'last 5 Firefox versions',
                          'Safari >= 6', 
                          'ie> 8
                     ] 
                  }),
              ]
            }
          }
```
效果
```
a{
     transition :transform 1s
}
```
```
a{
     -webkit-transition :-webkit-transform 1s;
     transition :-ms-transform 1s;
     transition :transform 1s
}
```
>注： 另外webpack还有一个autoprefixer-loader，但npm官网已将其标为【deprecated】，推荐使用通过postcss-loader的方式使用autoprefixer。
