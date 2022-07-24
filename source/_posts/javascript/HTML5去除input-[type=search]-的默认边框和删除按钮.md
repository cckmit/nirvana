---
title: HTML5去除input[type=search]的默认边框和删除按钮
date: 2021-03-11 21:12:40
categories: 前端
---

input [type=search]  是HTML新属性 ， 定义用于搜索的文本字段；


x-webkit-speech  属性：在GOOGLE浏览器上  还会显示一个小话筒

 autocomplete="off"  属性  关闭浏览器自动记录之前输入的值

webkit内核浏览器里 input 框类型如果是 type="search" 

那么将会有边框问题，border:0px 也不能起到作用；

解决办法是在CSS里写上

input[type="search"]{-webkit-appearance:none;} 

既可解决。
```
input[type=search] {
-webkit-appearance: textfield;
-webkit-box-sizing: content-box;
font-family: inherit;
font-size: 100%;
}
input::-webkit-search-decoration,
input::-webkit-search-cancel-button {
display: none;
}
```
