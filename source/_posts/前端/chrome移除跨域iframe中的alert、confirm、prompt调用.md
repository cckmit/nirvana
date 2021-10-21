---
title: chrome移除跨域iframe中的alert、confirm、prompt调用
date: 2021-09-13 21:12:40
category: javascript
---
在Chrome最近一次更新中（2021-08-03），有一条改动：

>移除跨域iframe中的alert、confirm、prompt调用

Chrome对此的解释是：网页内嵌的第三方页面弹窗可能让用户误以为这是当前页面弹出的弹窗，从而带来隐私风险。

如果从开发者的角度看待这条改动，显然是个breaking change。

全球不计其数的网站使用alert API弹出弹窗，这其中有相当一部分会作为iframe内嵌于其他网站中。

这条改动使得这部分数量庞大的网站的提示功能在Chrome浏览器下完全失效。

