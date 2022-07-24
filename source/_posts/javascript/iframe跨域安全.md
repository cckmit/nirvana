---
title: iframe跨域安全
date: 2022-03-10 16:28:03
categories: 前端
---
# 1.响应头X-Frame-Options

响应头[X-Frame-Options](https://cloud.tencent.com/developer/section/1190032?from=10680)是用来给浏览器指示允许一个页面可否在`<frame>`，`<iframe>`，`<object>`中展现的标记。网站可以使用此功能，来确保自己网站的内容没有被嵌套到其他网站中去，也从而避免了点击劫持 (clickjacking) 的攻击。

## 支持的指令

*   DENY 表示该页面不允许在frame中展示，即便是在相同域名的页面中嵌套也不允许。
*   SAMEORIGIN 表示该页面可以在相同域名页面的frame中展示。
*   ALLOW-FROM uri1,uri2 表示该页面可以在指定来源的frame中展示。

## 在nginx中配置

```
add_header X-Frame-Options DENY;
add_header X-Frame-Options SAMEORIGIN;
add_header X-Frame-Options "ALLOW-FROM http://www.a.com,http:///www.b.com";
```

## 兼容性

**ALLOW-FROM指令在除IE以外的很多浏览器中无效**，如在chrome中报错如下：

```
Invalid 'X-Frame-Options' header encountered when loading 'http://www.a.com':
 'ALLOW-FROM http://www.a.com' is not a recognized directive.
 The header will be ignored.
```

# 2.响应头Content-Security-Policy

响应头[Content-Security-Policy](https://cloud.tencent.com/developer/section/1189856?from=10680)允许网站管理员控制允许用户代理为给定页面加载的资源。除少数例外，策略主要涉及指定服务器源和脚本端点。这有助于防止跨站点脚本攻击（XSS）。

## 支持的指令

*   frame-ancestors uri1 uri2 允许一个页面可否在`<frame>`，`<iframe>`，`<object>`，`<embed>`，或`<applet>`中展现。 将此指令设置’none’为类似于`X-Frame-Options: DENY`

## 在nginx中配置

```
add_header Content-Security-Policy "frame-ancestors ‘none’";
add_header Content-Security-Policy "frame-ancestors http://www.a.com http://www.b.com";
```

## 兼容性

**IE浏览器不支持**

# 3.子域名跨域

site1.a.com

```document.domain = "a.com";```

site2.a.com

```document.domain = "a.com"```

将不同子域名的站点document.domain设置为相同的基础域名，则可实现跨域访问
