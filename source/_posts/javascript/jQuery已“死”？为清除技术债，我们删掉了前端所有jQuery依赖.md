---
title: jQuery已“死”？为清除技术债，我们删掉了前端所有jQuery依赖
date: 2022-06-01 16:28:03
categories: 前端
---
近期，英国公共部门信息网站 GOV.UK 前端开发主管 Matt Hobbs 宣布该公司删除了 jQuery 作为所有前端应用程序的依赖项，这意味着“在所有 13 个 FE 应用程序中，JS 大小减少了 32 KB（31% ～49% 之间）”。
图片
一些关键指标得到优化
Matt 也在推特上分享了几组数据，说明了在删除 jQuery 后一些关键指标得到了优化。
移除页面标签限制并查看所有页面 RUM 数据， 75% 用户的页面都有类似的下降：
![](https://upload-images.jianshu.io/upload_images/10024246-61d8d700f6796f4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 75% 的页面中仅检查 Android 用户，可以看到 JS 长任务改进了 7%：
![](https://upload-images.jianshu.io/upload_images/10024246-1c557f30fd129770.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
50% 用户的移动设备上的 JS Long Tasks 有 10% 的改进：
![](https://upload-images.jianshu.io/upload_images/10024246-4f7bf3595b54f441.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而对于 95% 用户，阻塞时间则减少了 10% ：
![](https://upload-images.jianshu.io/upload_images/10024246-358d688b907568da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
“这些用户会遇到严重不利的网络和设备条件，每一次性能提升对他们来说尤其重要。”Matt 说道。
根据 Matt 说法，删除 jQuery 的本意是清理技术债。“它最初是为了支持浏览器而存在的，但随着时间的推移，情况发生了变化，所以 bits 可以被删除。我想在这之后会重新评估，看看还有什么是不再需要的。” Matt 表示。