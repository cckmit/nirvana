---
title: 阿里巴巴主导的 Weex 因活跃度不够从 Apache Incubator 退休
date: 2021-06-10 16:28:03
categories: 资讯
---
[根据 Apache Incubator 邮件列表的信息](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Flists.apache.org%2Fthread.html%2Freb2523a568b5ce5cfc1b8017c20106221f0ab689ebcf3cb42feb8a54%2540%253Cgeneral.incubator.apache.org%253E)，上周发起的关于 Weex 是否从 Apache Incubator 退休的投票已结束。投票结果显示，由于没有[-1]否决票，所以 Weex 从 Apache Incubator 退休的投票获得了 IPMC 的一致通过。

![](https://upload-images.jianshu.io/upload_images/10024246-af70e8e94120a0b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Weex 从 Apache Incubator 退休并不意味着停止维护，仅表示它不再在 Apache Incubator 中开发（与 TLP 项目移动至 [Attic](https://www.oschina.net/news/112808/apache-moved-to-the-attic) 类似），详情查看 [Guide to Retirement](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fincubator.apache.org%2Fguides%2Fretirement.html)。

此外，从之前邮件列表中的[讨论](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Flists.apache.org%2Fthread.html%2Frcaf67836a77ad3bebcafffc9e853bcd043a844e123e06ac8e71c9f4d%2540%253Cdev.weex.apache.org%253E)可以看到，Weex 在阿里巴巴（尤其是淘宝）中仍被广泛使用，许多开发者仍在开发 Weex，并有长期计划来对其进行改进。所以 Weex 从 Apache Incubator 退休后也会继续开发。

此次投票的发起人，同时也是 Weex 项目的导师姜宁 ([@WillemJiang](https://my.oschina.net/u/3569919)) [表示](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fwww.zhihu.com%2Fquestion%2F459369570%2Fanswer%2F1886114582)，Weex 从 Apache Incubator 退休的主要原因是项目活跃度不够。这里的活跃度指的是 Weex 在 Apache 社区中的活跃情况，姜宁提到 Weex 的很多 PPMC 成员都不活跃，例如没有提交 IPMC Chair 需要的社区报告。不活跃的氛围导致项目发布新版本时也没有足够的人来进行投票（官网和 GitHub 上显示的最新版本是2019年发布的 [0.28](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fgithub.com%2Fapache%2Fincubator-weex%2Freleases%2Ftag%2F0.28.0)）。

![](https://upload-images.jianshu.io/upload_images/10024246-eac2fdc67fb7bf4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

根据 ASF 董事吴晟 ([@wu-sheng](https://my.oschina.net/wusheng)) 的[说法](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Ftwitter.com%2Fwusheng1108%2Fstatus%2F1393011558289051651)，Weex 似乎也是中国首个从 Apache Incubator 退休的项目。

![](https://upload-images.jianshu.io/upload_images/10024246-7e4ed45f7d5166fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[Weex](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fweex.apache.org%2Fzh%2F) 于2016年[正式开源](https://www.oschina.net/news/72703/weex-web-native)，定位是使用 Web 来开发高性能原生应用的框架。Weex 致力于使开发者能基于通用跨平台的 Web 开发语言和开发经验，来构建 Android、iOS 和 Web 应用。简单来说，在集成 WeexSDK 之后，开发者可以使用 JavaScript 语言和前端开发经验来开发移动应用。

![](https://upload-images.jianshu.io/upload_images/10024246-43dc96bc94d25aba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在再回去看 Vue.js 作者尤雨溪当时对 Weex 的[评价](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fwww.zhihu.com%2Fquestion%2F37636296%2Fanswer%2F72881168)，似乎一切早已注定……

![](https://upload-images.jianshu.io/upload_images/10024246-01a451e251c17421.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>**PS:**16年团队在做多端时，在选择weex还是react-native，考虑了很多，最终选择了，react-native作为主要研究方向，现在回顾下，有庆幸，有惋惜。希望以后国内的开源项目越来越好！