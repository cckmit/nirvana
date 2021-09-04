---
title: iframe的document操作
date: 2021-06-10 16:28:03
category: javascript
---
## 1.获取页面上的所有iframe标签遍历获取每一个iframe
获取所有的iframe标签
```
let iframes = document.getElementsByTagName("iframe");
遍历获取单个iframe
for (let i = 0; i < iframes.length; i++) {
       let iframeId = iframes[i].id;
       let currentIframe = document.getElementById(iframeId);
}
```
取完整iframe元素必须用getElementById的方法获取。
## 2.获取iframe下document元素
```
let currentDoc = currentIframe.contentDocument || currentIframe.contentWindow.document  
```
这里主要拿到iframe的document操作元素，有些浏览器可以直接contentDocument获取document操作元素，有些需要通过contentWindow.document获取
## 3.获取iframe中输入框
```
let inputs = currentDoc.getElementsByTagName("input");
```
这样就能获取iframe所有的输入框标签。

*注意：当iframe跨域的时候，就无法获取iframe的document操作。
