---
title: meta基础知识
date: 2021-06-10 16:24:50
categories: 前端
---
**H5页面窗口自动调整到设备宽度，并禁止用户缩放页面**

```
 name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />
```

**忽略将页面中的数字识别为电话号码**

```
 name="format-detection" content="telephone=no" />
```

**忽略`Android`平台中对邮箱地址的识别**

```
 name="format-detection" content="email=no" />
```

**当网站添加到主屏幕快速启动方式，可隐藏地址栏，仅针对`ios`的safari**

```
 name="apple-mobile-web-app-capable" content="yes" />
```

**将网站添加到主屏幕快速启动方式，仅针对ios的safari顶端状态条的样式**

```
 name="apple-mobile-web-app-status-bar-style" content="black" />
```

