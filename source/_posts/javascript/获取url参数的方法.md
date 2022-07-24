---
title: 获取url参数的方法
date: 2022-02-04 16:28:03
categories: 前端
---
## 正则表达式获取url
常规使用正则表达式去获取url参数的代码
```
function getParams(url, params){
      var res = new RegExp("(?:&|/?)" + params + "=([^&$]+)").exec(url);
      return res ? res[1] : '';
}

// url: xx.com?id=2&isShare=true
const id = getParams(window.location.search, 'id')

console.log(id) // 2
```
## URLSearchParams方法
使用URLSearchParams方法实现获取url参数

```
const urlSearchParams = new URLSearchParams(window.location.search);
const params = Object.fromEntries(urlSearchParams.entries());

console.log(params) // {id: '2', isShare: 'true'}
console.log(params.id) // 2
复制代码
```

只需要以url作为参数传入，并且创建`URLSearchParams`的一个实例对象，然后调用`entries()`这个方法，返回一个迭代协议[iterator](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)，该协议支持可以遍历所有支持健/值对的列表

此时还需要通过`Object.fromEntries()`这个方法去把该健/值对的列表，然后我们就可以愉快地使用获取到的参数啦

##### URLSearchParams的兼容性

搜了一下URLSearchParams的兼容性查询，[详情请点击](https://caniuse.com/?search=URLSearchParams)

![](https://upload-images.jianshu.io/upload_images/10024246-14885064677a1950.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


其他现代浏览器的兼容性都杠杠的，如果是不需要兼容IE的项目，可以放心使用

如果实在要兼容IE，可以使用`url-search-params-polyfill`这个插件去使用啦

安装方式：

```
npm install url-search-params-polyfill --save
```

使用方式:

```
const params = new URLSearchParams("id=2&isShare=true");
```
