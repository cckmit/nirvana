---
title: 下载量和Vue一样大的开源软件被作者恶意破坏，数千款应用受到牵连
date: 2022-01-10 16:28:03
categories: 前端
---

>开源的黑暗面：faker.js 到底发生了什么？

流行开源包“colors”与“faker”的用户们最近刚刚遭遇一场意外，毫无征兆的破坏导致应用程序在使用这些包后开始输出无法理解的乱码数据。这背后的原因竟然是开源软件包的作者 Marak Squires 故意引入了一个无限循环，让数千个依赖于“colors”与“faker”包的应用程序全面失控。

colors.js 是一个用于处理颜色的 JavaScript 库，而 faker.js 是一个用于生成假数据的 JavaScript 库。在构建和测试应用程序时，假数据很有用，faker.js 可以为各个领域生成虚假数据，包括地址、商业、公司、日期、财务、图像或名称。

这两个包特别受开发者欢迎，其中单是 colors 包在 npm 上就拥有每周 2000 多万次下载量，依赖于它的项目近 19000 个。此外，faker 在 npm 上每周下载量也超过 280 万次，相关项目超 2500 个，faker 的受欢迎程度可媲美于 Vue。因为这些开源软件的应用特别广泛，所以这个事件影响也特别深远。

![](https://upload-images.jianshu.io/upload_images/10024246-b8b4c34690b36747?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

开源革命，还是开源暴动？

这两套高人气开源 npm 包“colors”（在 GitHub 上名为 colors.js）与“faker”（在 GitHub 上名为 faker.js）背后的开发人员故意在代码中引入了错误内容，相应提交进一步对依赖这些包的成千上万应用程序造成影响。

就在昨天，Amazon 云开发工具包（aws-cdk）等开源项目用户，突然发现自己的**应用程序开始在控制台上疯狂输出乱码消息。**

这些消息中包含大量“LIBERTY LIBERTY LIBERTY”（自由）字样，之后还跑着一大堆非 ASCII 字符：

![](https://upload-images.jianshu.io/upload_images/10024246-9c1bb74bc0d3f23b?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

面对“faker”与“colors”项目倾泻出的垃圾数据，用户感到震惊错愕 (GitHub)

最初，用户怀疑这些项目使用的“colors”与“faker”包遭到了恶意入侵，类似于去年 coa、rc 和 ua-parser-js 等包被攻击者劫持的状况。但事实证明，令 colors 与 faker 陷入混乱的其实是由合法开发者**故意提交的错误代码。**

开发者 Marak Squires 向 colors.js 包的 v1.4.44-liberty-2 版本中添加了“新的美国国旗模块”，此项变更随后被推送至 GitHub 与 npm。

![图片](https://upload-images.jianshu.io/upload_images/10024246-0d1a8639d0ef3236?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

colors.js 恶作剧提交出自“Marak”之手 (GitHub)

新代码中引入的无限循环会没完没了地运行，任何使用“colors”的应用程序都会在控制台上无休止地输出由非 ASCII 字符序列组成的乱码。

同样的，faker 的恶作剧版本“6.6.6”也被发布到了 GitHub 和 npm 之上。这位开发者还语带嘲讽地表示，“我们注意到 v1.4.44-liberty-2 版本的 colors 中存在一项 Zalgo bug。”“我们正在努力解决这个问题，请大家静待解决方案的发布。”所谓 Zalgo 文本，指的就是因某些故障引发的非 ASCII 字符。

这位开发者的举动似乎是在故意报复，希望反抗那些长期依赖于免费和社区支持软件、但却从不向社区做出回馈的大型企业和其他利用开源项目进行商业化盈利的用户。

2020 年 11 月，Marak 就已经警告称不会再“无偿工作”来支持那些商业巨头，并强调这些业务实体面前只有两条路：要么选择项目分叉，要么以每年“六位数”的薪酬补偿开源开发人员。

![](https://upload-images.jianshu.io/upload_images/10024246-6cdb926b309768ae?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这位开发者之前曾写道，“诸位，我不会再用自己的无偿工作来支持那些财富五百强（以及其他小型公司）了。言尽于此。”“所以现在只有两个选择，要么给我发一份年薪六位数的用工合同，要么赶紧分叉项目、找其他人接手。”

![](https://upload-images.jianshu.io/upload_images/10024246-862bf17bb5ca6676?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

有趣的是，我们发现“faker”项目在 GitHub repo 上的 README 页面（https://github.com/marak/Faker.js/）也被 Marak 改掉了，里面赫然出现了“Aaron Swartz 到底经历了什么？”的字样。

Swartz 是一位杰出的开发人员，他帮助建立了 Creative Commons、RSS 和 Reddit。他的贡献对几乎所有的 Web 开发人员都产生了深远的影响。2013 年，年仅 26 岁的 Swartz 在一场法律纠纷中自杀身亡。

为了让所有人都能免费访问信息，这位黑客主义者从麻省理工学院的校园网络 JSTOR 数据库中下载到数百万篇期刊文章。据称，他采用的方法是反复轮换自己的 IP 与 MAC 地址以绕过校方和 JSTOR 设置的技术屏蔽方案。但此番举动也导致 Swartz 面对违反《计算机欺诈与滥用法》的指控，一旦罪名成立，他最高可能被判处 35 年监禁。

事件引发轩然大波

Marak 的大胆举动旋即引发轩然大波，各界纷纷就此事发声。

部分开源软件社区成员赞扬了这位开发者的勇敢行为，但也有人对他的过激举动表示震惊。一位用户在推文中写道，“很明显，colors.js 的作者因为拿不到报酬而抓狂……所以他决定每当有用户加载他的包时都输出一面美国国旗……这是什么脑回路？”

也有人觉得这是“又一个开源开发者造成的流氓案件”，信息安全厂商 VessOnSecurity 则公开指责此举“不负责任”，并强调：“如果你不想有人用免费代码组织商业业务，那别发布这类代码不就好了？这种无差别打击的行为伤害到的不止是大企业，更是每一位使用开源代码的用户。此类行径只会打击用户的版本更新热情，让他们在每次升级时都提心吊胆。”

根据相关报道，GitHub 已经冻结了这位开发者的账户，而群众们对此也是议论纷纷：

![](https://upload-images.jianshu.io/upload_images/10024246-9cb871179488795b?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> NPM 已经将 faker.js 包回滚至先前版本，GitHub 则暂停了我对所有公共及私有项目的访问权限。那可是上百个项目，现在都访问不了了。#AaronSwartz  --@marak

软件工程师 Sergio Gómez 回应称，“删改自己的代码也违反到 GitHub 的服务条款了？这是赤裸裸的绑架！我们最好做好分散托管软件源代码的准备。”

另一位用户也发布推文称，“不知道究竟是怎么回事，但我自己所有的项目都托管在 GitLab 的私有实例上，以免遇上类似的状况。永远不要相信任何互联网服务商。”

一位名叫 Piero 的开发者则指出，“Marak 搞乱了 faker 和 colors、影响到无数项目，难道还指望着自己能不受一点牵连？”

需要提醒大家一点，Marak 的此次过激行为发生在不久前影响巨大的 Log4j 漏洞事件之后。作为一套重量级开源库，Log4j 在不同企业及商业实体开发的各类 Java 应用程序当中都有广泛使用。而 Log4shell 漏洞的曝光引发越来越多 CVE，不少开源维护者不得不在休假期间无偿帮助修复这些免费项目。

于是开源业界开始普遍担忧，认为大企业们已经习惯于“压榨”开源成果、不停消耗，但却没有给予足够的回报来支持这些志愿放弃空闲时间来维持关键项目的贡献者。面对网民和 Bug 赏金猎人们的指责之词，也有人愤然回击，强调 Log4j 的维护者们“已经在夜以继日地构建缓解方案，包括修复、撰写文档、提交 CVE 以及回复质询等等。”

有用户在推文中写道，“对于此次 colors.js/faker.js 作者破坏自有软件包的回应，恰恰说明很多企业开发者认为自己在道德上有权无偿享用开源开发者的劳动成果、且无需给出任何回报。”“虽然免费开源软件运动及其目标值得称赞，但它最终让许多非常有才华的人幻想破灭且变得贫穷，因为开源实际上并不是一种可行的商业模式。”

至于开源软件生态到底能不能长久存续，恐怕只有时间能给出答案。

与此同时，这里要提醒 colors 与 faker 两大 npm 项目用户务必使用安全的包版本。比较稳妥的方法是降级至上个版本的 colors（例如 1.4.0）与 faker（例如 5.5.3）。

>**参考链接：**
https://www.bleepingcomputer.com/news/security/dev-corrupts-npm-libs-colors-and-faker-breaking-thousands-of-apps/
