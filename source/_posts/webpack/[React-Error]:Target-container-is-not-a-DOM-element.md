---
title: '[React Error]: Target container is not a DOM element'
date: 2021-06-10 16:46:33
categories: webpack
---
webpack在后端对React组件进行渲染的时候，浏览器控制台报错：

`compose.js:822 Uncaught Invariant Violation: _registerComponent(...): Target container is not a DOM element.`

我很奇怪，webpack可以正常将我的React组件使用babel转换，我的组件代码如下：
```
import React from 'react'
import ReactDOM from 'react-dom'
import './compose.less'

class App extends React.Component {
  render() {
    return (
      <p className="compose-font">
        Little Bitch
      </p>
    )
  }
}
ReactDOM.render(<App />, document.getElementById('app'));
```

```
var config = {
    entry: './main.js',
     
    output: {
       path:__dirname,
       filename: 'index.js',
    },
    
    mode: "development", 

    devServer: {
       inline: true,
       port: 4444
    },
    resolve: {
        extensions: [ '.js', '.jsx', '.css', '.json'], // 配置默认扩展名
        modules: ['node_modules']
    },
    module: {
        rules: [ {
          test: /\.js|jsx?$/,
          exclude: [/node_modules/],
            loaders: 'babel-loader',
            options: {
                presets: ["@babel/env"]
            }
       }]
       
    }
     
 }
 
 module.exports = config;
```
html页面
```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
  </head>
    <script src="./index.js"></script>
  <body>
    <div id="app"></div>
  </body>
</html>
```
出现此错误的原因是我将webpack生成的js文件放在了head，此时DOM还没有建立完毕，因此出现` not a DOM element `的错误，所以将js文件放在HTML底部就可以了。
另外一个可能出错的点是`document.getElementById('app')`中的id不对应，造成无法在页面中找到对应的节点
