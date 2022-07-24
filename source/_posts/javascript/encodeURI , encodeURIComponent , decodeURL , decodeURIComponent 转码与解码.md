---
title: encodeURI , encodeURIComponent , decodeURL , decodeURIComponent 转码与解码
date: 2022-02-13 16:28:03
categories: 前端
---
**一、这四个方法的用处**

**1、用来编码和解码URI的**

统一资源标识符，或叫做 URI，是用来标识互联网上的资源（例如，网页或文件）和怎样访问这些资源的传输协议（例如，HTTP 或 FTP）的字符串。除了encodeURI、encodeURIComponent、decodeURI、decodeURIComponent四个用来编码和解码 URI 的函数之外 ECMAScript 语言自身不提供任何使用 URL 的支持。

**2、URI组成形式**

一个 URI 是由组件分隔符分割的组件序列组成。其一般形式是：

*Scheme* : *First* / *Second* ; *Third* ? *Fourth*

其中斜体的名字代表组件；“:”, “/”, “;”，“?”是当作分隔符的**保留字符**。

**3、有和不同？**

[encodeURI](https://www.w3.org/html/ig/zh/wiki/ES5/%E6%A0%87%E5%87%86_ECMAScript_%E5%86%85%E7%BD%AE%E5%AF%B9%E8%B1%A1#encodeURI) 和 [decodeURI](https://www.w3.org/html/ig/zh/wiki/ES5/%E6%A0%87%E5%87%86_ECMAScript_%E5%86%85%E7%BD%AE%E5%AF%B9%E8%B1%A1#decodeURI) 函数操作的是完整的 URI；这俩函数假定 URI 中的任何保留字符都有特殊意义，所有不会编码它们。

[encodeURIComponent](https://www.w3.org/html/ig/zh/wiki/ES5/%E6%A0%87%E5%87%86_ECMAScript_%E5%86%85%E7%BD%AE%E5%AF%B9%E8%B1%A1#encodeURIComponent) 和 [decodeURIComponent](https://www.w3.org/html/ig/zh/wiki/ES5/%E6%A0%87%E5%87%86_ECMAScript_%E5%86%85%E7%BD%AE%E5%AF%B9%E8%B1%A1#decodeURIComponent) 函数操作的是组成 URI 的个别组件；这俩函数假定任何保留字符都代表普通文本，所以必须编码它们，所以它们（保留字符）出现在一个完整 URI 的组件里面时不会被解释成保留字符了。
![](https://upload-images.jianshu.io/upload_images/10024246-8744586f8f1106de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
