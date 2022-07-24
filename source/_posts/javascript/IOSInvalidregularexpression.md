---
title: IOS Invalid regular expression： invalid group specifier name
date: 2021-03-11 21:12:40
categories: 前端
---

如果正则表达式中包含零宽断言的话 , 在安卓手机上正常 , 但是在ios上会报以下错误
```
Info Warn Error SyntaxError: Invalid regular expression: invalid group specifier name
```
`常用零宽断言：?<=、?<!、?!、?=`;
 ```
let reg = new RegExp(`(?<=\\b${key}=)[^&]*`) , str = this.params || '', target = str.match(reg); if(target) {  return target[0] }
```
这种写法在安卓是正常的，IOS就不行，因为该正则包含了零宽断言，会导致页面空白，打不开，需要相对应的转换才能使用
```
let reg = new RegExp(`(^|&)${key}=([^&]*)(&|$)`),str = this.params || '', target = str.match(reg);if(target) { return target[2]}
```
