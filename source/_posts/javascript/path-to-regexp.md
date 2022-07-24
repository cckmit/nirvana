---
title: url 的正则表达式：path-to-regexp
date: 2021-03-20 17:46:44
categories: 前端
---
## 概述
该工具库用来处理 url 中地址与参数，能够很方便得到我们想要的数据。

js 中有 RegExp 方法做正则表达式校验，而 path-to-regexp 可以看成是 url 字符串的正则表达式。

## 使用
第三方库，使用前先进行安装：
```
$ npm install path-to-regexp
```
在 js 中使用：
```
const pathToRegexp = require('path-to-regexp');
```
## API 介绍
##### 1. pathToRegexp()
作用：这里这个方法可以类比于 js 中 new ExpReg('xxx')。
```
var pathToRegexp = require('path-to-regexp')

var re = pathToRegexp('/foo/:bar')
console.log(re);	
```
打印结果如下:
```
/^\/foo\/((?:[^\/]+?))(?:\/(?=$))?$/i
```
要注意两点，一点是我们自己的 url 地址，一条是匹配规则。

##### 2. exec()
作用：匹配 url 地址与规则是否相符。
```
var pathToRegexp = require('path-to-regexp')

var re = pathToRegexp('/foo/:bar');     // 匹配规则
var match1 = re.exec('/test/route');    // url 路径
var match2 = re.exec('/foo/route');     // url 路径

console.log(match1);
console.log(match2);
```
打印结果如下：
```
null
[ '/foo/route', 'route', index: 0, input: '/foo/route' ]
```
说明：

上述代码中，第一个 url 路径与匹配规则不相符返回 null，第二个 url 路径与匹配规则相符，返回一个数组。

##### 3. parse()
作用：解析 url 字符串中的参数部分（:id）。
```
var pathToRegexp = require('path-to-regexp');

var url = '/user/:id';
console.log(pathToRegexp.parse(url));
```
打印结果如下：
```
[ '/user',
  { name: 'id',
    prefix: '/',
    delimiter: '/',
    optional: false,
    repeat: false,
    partial: false,
    pattern: '[^\\/]+?' } ]
```
说明：返回一个数组，从第二个数据可以就可以得到 url 地址携带参数的属性名称（item.name）。

当然，url 中携带参数出了 :id 这种形式，还有 /user?id=11 这种，使用 parse() 方法解析自行查看结果。

##### 4. compile()
作用：快速填充 url 字符串的参数值。
```
var pathToRegexp = require('path-to-regexp')

var url = '/user/:id/:name'
var data = {id: 10001, name: 'bob'}
console.log(pathToRegexp.compile(url)(data))
```
打印结果：
```
/user/10001/bob
```