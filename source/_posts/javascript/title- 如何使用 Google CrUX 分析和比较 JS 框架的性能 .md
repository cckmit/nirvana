---
title: 如何使用 Google CrUX 分析和比较 JS 框架的性能
date: 2022-06-02 16:28:03
categories: 前端
---
>在美国本土流量前 100 万的站点中（按流量统计），Vue 的性能追平了 React。

最近几年，框架已经成为 Web 开发领域的标杆，其中的排头兵当数 React。事实上，我们已经很少见到有人不用任何框架或者 CMS 之类的平台，就可以开发新的网站或 Web 应用程序。

虽然 React 的口号是“一套用于构建用户界面的 JavaScript 库”，丝毫没提框架的事，但我认为事实已经确定：大部分 React 开发者都把它当成框架来看，当成框架来用。至少，大家会把 React 当成整体应用程序框架中的一部分，例如 NextJS、Gatsby 或者 RemixJS 等。

但从 Web 开发者的角度出发，这类框架带来开发体验改进的背后，究竟让我们付出了怎样的代价？尤其是，最终用户又为 Web 开发圈的这种框架依赖性付出了什么？

在本文中，我们将根据 Google Chrome 用户体验报告（简称 CrUX）收集到的一线数据，分析与各类框架相关的性能成本。这些信息有趣且具有借鉴意义，通过这些数据分析，或许能给前端及全栈开发者，从当前众多框架和平台中开辟出一条清晰的选择思路。

但首先得强调一点，查看数据时千万不要把相关性和因果性混为一谈。当使用某种特定框架时，其网页性能比另一框架的网页性能更好或更差，并不能代表框架本身的优劣。比如，可能是因为某些框架常被用于构建重量级网站而产生了一些影响。

换句话说，这些数据最多能帮助大家在设计前端项目时，更合理地考察并选择框架方案。只要影响不大，我们不妨选择那些性能比更好的框架。

## 从一线收集 Core Web Vitals

前文已经提到，此次分析的主要数据源是 Google CrUX。作为一套云端数据库，CrUX 会实时从 Chrome 浏览器会话中收集真实用户指标（RUM）。只要大家选择同步浏览历史记录，不设置同步密码，并且启用“使用统计报告”功能，那么每当我们用 Chrome 加载网页时，关于会话的信息都会被自动上报给 CrUX。尤其是收集的测量值包括为每个会话测量的三项，它们被统称为核心页面指标，即 Core Web Vitals（CWV）。

*   最大内容绘制 （LCP）

*   首次输入延迟 （FID）

*   累积布局偏移 （CLS）

近年来，这些指标已经成为现代 Web 性能分析的基石。

针对每项指标，谷歌分别定义了：好（绿色）、一般 / 须改进（橙色）和差（红色）三个数值范围。

![](https://upload-images.jianshu.io/upload_images/10024246-d4784cd13deb33b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从 2021 年 6 月开始，这些指标因素被纳入谷歌搜索排名，这也让几个小小的数字获得了较高的江湖地位。

除了为各个会话收集现场数据外，谷歌还使用 WebPageTest 工具对各网站执行综合衡量。这些实验室测量值将由 Wappalyzer 服务收集到另一个名为 HTTP Archive 的在线存储库内。

之后，谷歌可以使用其 BigQuery 项目对这两个集合执行查询。但要从这些数据中获取洞察力，最简单的方法还是使用 Rick Viscomi 创建的 Core Web Vitals 技术报告。（Rick 是谷歌公司的开发关系主管工程师，负责管理 CrUX 和 HTTP Archive）。该报告提供了一种方法，即以交互方式绘制各类基于 Web 的平台与技术相关性能数据，并轻松地对内容做出相互比较。

在本文中，我们主要通过 Core Web Vitals 技术报告中的数据提炼分析见解。

在数据分析过程中，我们需要注意以下几点：

*   虽然现场数据收集自各个页面，但技术报告本身会按来源显示，且来源值为该来源中所有页面值的聚合，最终结果为基于页面流量计算出的加权平均值。

*   另一方面，良好来源的比率没有加权。这意味着即使流量相对较少，但只要性能表现不错，则该来源在报告中就等同于性能同样良好，但流量明显更高的其他来源。我们可以按流行度排名对来源进行过滤，借此拉开低流量源与高流量源之间的差距。

*   HTTP Archive 仅分析各来源的主页。这意味着框架识别只针对主页进行，并将该来源内的所有其他页面全部视为该框架的性能聚合——无论子页面中是否继续使用该框架，或者是否配合使用到其他框架。

*   各子域被视为独立的不同来源。

## 比较各 JavaScript 框架的 CWV

让我们先从各 JavaScript 框架的性能说起。具体来看，要看使用这些框架的网站当中，有多大比例在三项 CWV 指标上都得到良好评价。三者全“好”，代表该网站应该是在性能上带来了不错的用户体验，也有资格获得谷歌搜索排名的优先展示。

为了消除地域分布对不同框架之间造成的影响，我对数据进行了过滤，仅涉及美国本土会话数据。图中的 ALL 线是指 CrUX 的所有网站（而非仅仅是使用了框架的网站），主要用于参考和比较。

![](https://upload-images.jianshu.io/upload_images/10024246-21238cccbe2d2ce7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土，三项 CWV 指标均为绿色的移动端会话所使用的领先框架所占百分比。

![](https://upload-images.jianshu.io/upload_images/10024246-40b10d57d006a0a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土，三项 CWV 指标均为绿色的桌面端会话所使用的领先框架所占百分比。

注意：在我们的分析中，移动端并不包含 iOS 设备，例如 iPhone。这是因为 iOS 上的 Chrome 其实就是 Safari 内核换皮，所以根本不测量或上报 CWV（iOS 上的所有浏览器都是如此，全部属于 Safari 换皮）。

首先，我们可以看到不同的框架会产生截然不同的结果。而且无论好坏，各框架在过去一年中的性能表现基本保持稳定（其中 Preact 是个例外，后文将具体解释变化的原因）。这表明评分确实有意义，而非侥幸或统计异常下的结果。

根据测量值，我们发现不同框架确实对应着不同的性能成本：通过与 ALL 基准相比，可以看到只要使用框架，其性能一般就要落后于不用框架构建的网站。虽然 React、Preact 和 Svelte 等框架已经能让性能接近整体平均水平，但有趣的是，没有任何框架能提供比其他框架更好的性能。

React 和 Preact 基本可以说是并驾齐驱——考虑到 Preact 比 React 小得多，所以这样的结论可能有点出乎意料。Preact 只有约 4 KB，而 React 大概是 32KB（二者都经过压缩）。很明显，在大多数情况下，28KB 的下载量差值并没有多大影响。Preact 的缔造者 Jason Miller 最近也就此做出说明：

Preact 并不直接关联于任何特定 SSR 或者服务解决方案，而这些才是决定 CWV 的关键。虽然 Preact 不可避免会对 CWV（实际就是其中的 FID，即首次输入延迟）造成影响，但远不如页面交付中涉及的其他技术那么直接。  — Jason Miller (@_developit)2022 年 2 月 11 日 

Vue 和 AngularJS 处于第二梯队——在移动端获得良好 CWV 的概率比第一梯队低 20% 到 25%，而桌面端的概率则低 15% 到 20%（其中同样不包含 iOS）。也就是说，Vue 似乎正在逐步改进，慢慢缩小与 React 之间的性能差距。

Preact 性能的大幅下降跟框架自身无关，主要原因来自 Wappalyzer 对 Preact 的识别方式。很遗憾，Wappalyzer 无法识别 2021 年 11 月之前使用 Preact 构建的大部分网站，因此在此时间点之前的 Preact 统计结果有错，可以直接忽略。

## 通过热门网站数据对比领先框架

当我们把目光转向全美排名前 100 万的站点（按流量统计）时，有趣的变化出现了：Vue 几乎紧跟在 React 身后，可以看到热门网站中有更多使用 Vue 的高性能案例；而对于 React，表现良好的站点反而意外有所减少：

![](https://upload-images.jianshu.io/upload_images/10024246-fcfed0afacf6c7d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土流量前 100 万的站点中，三项 CWV 指标均为绿色的移动端会话所使用的领先框架所占百分比。
![](https://upload-images.jianshu.io/upload_images/10024246-e5369f79a439e8e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土流量前 100 万的站点中，三项 CWV 指标均为绿色的桌面端会话所使用的领先框架所占百分比。

## 通过各指标分析对比框架性能

除了关注整体 CWV 之外，技术报告还对各项指标展开逐一分析。我们先从 FID 开始：

![](https://upload-images.jianshu.io/upload_images/10024246-f4deb0c09f212cc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土，FID 指标为绿色的移动端会话所使用的领先框架所占百分比。

大家可能已经预料到使用框架的网站在代表响应性能的 FID 上会表现较差，主要是因为框架中会大量使用 JS 代码。但实际情况并非如此——事实上，FID 指标并没有拉开差距，所有框架都拿到了近乎完美的得分。

即使是查看集合中所有网站的聚合，结论也依旧不变。出于这个原因，谷歌正研究推出其他更好的响应性指标，而且已经通过测试收集反馈意见。

所以，观察 LCP 指标明显更有意义：

![](https://upload-images.jianshu.io/upload_images/10024246-a79d61dbf6c9bdc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土，LCP 指标为绿色的移动端会话所使用的领先框架所占百分比。

有趣的是，LCP 分数与 CWV 整体高度一致，只是分布范围更大：ALL、React、Preact 和 Svelte 大约高出 10%，而 Vue 和 AngularJS 则基本保持不变。而将统计范围限制在前 100 万个站点时，会看到 Preact 和 Svelte 的成绩略有提升，但 React 基本保持不动，所以被 Vue 迎头赶上。

![](https://upload-images.jianshu.io/upload_images/10024246-7e744ecbcba5f977.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土流量排名前 100 万的网站中，LCP 指标为绿色的移动端会话所使用的领先框架所占百分比。

最后，我们来看看 CLS，同样是先从全体网站开始：

![](https://upload-images.jianshu.io/upload_images/10024246-f8b242f23baab00c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土，CLS 指标为绿色的移动端会话所使用的领先框架所占百分比。

接下来是排名前 100 万的网站：

![](https://upload-images.jianshu.io/upload_images/10024246-6fc7ab6cb0433b5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土流量排名前 100 万的网站中，CLS 指标为绿色的移动端会话所使用的领先框架所占百分比。

这里可以看到，React 和 Preact 的性能都出现了大幅下降，特别是 React，导致 Vue 成功完成双杀反超。

换句话说，对于 Vue，如果我们只着眼于高流量顶级站点，其 LCP 和 CLS 的良好比率都会提高。另一方面，React 的 LCP 基本保持不变，CLS 反而有所下降。

使用 React 的顶级网站之所以出现性能下降，很可能是因为页面上的广告变多了。广告通常会对 CLS 产生不利影响，因为它们的出现会挤压其它内容的资源空间。但我倒觉得事情未必如此，因为这解释不了 React 跟其他框架之间的显著差异。另外，照这样推断，更多广告同样也会影响到 LCP，但可以看到 LCP 指标基本保持不变。

## Web 应用程序框架的性能指标

那 NextJS 和 NuxtJS 是怎么回事？为什么它们没能像 Gatsby（以及 Wix）那样带来预期中的全方位性能优势？通过对各项指标的单独分析，也许我们能够破解这个谜团。

跟之前一样，FID 指标对于整体良好性能占比基本没有影响，因为所有框架都达到了 97% 以上的良好 FID 比率。

但在比较 CLS 比率时，事情变得有趣起来：

![](https://upload-images.jianshu.io/upload_images/10024246-0af7ed3e34438ed8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土，CLS 指标为绿色的移动端会话所属网站的所占百分比。

可以看到，NextJS 实际上超越了 React，但 Gatsby 仍然保持领先。而 NuxtJS 则介于 Vue 和 React 之间。

因为所有框架的良好 FID 比率和 CLS 比率都很接近，所以可以看出框架间的差异主要集中在 LCP 身上：

![](https://upload-images.jianshu.io/upload_images/10024246-8f7705f4ec1e7943.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在美国本土，LCP 指标为绿色的移动端会话所属网站的所占百分比。

跟预期一样，可以看到 Gatsby 仍然明显优于 React，而 React 又整体优于 Next.js。鉴于 NextJS 使用的是 SSR/SSG 和现代图像格式，所以落后于 React 着实令人意外。所以在使用 NextJS 时，请务必注意这一点。

NuxtJS 最近几个月在性能上取得了重大进展，并在 LCP 良好率上超越了 NextJS，总体上已经跟 React 基本相当。

下面再来看 LCP 差异能不能用图像下载数据量来解释，毕竟图像越大，对 LCP 的负面影响就越严重：

![](https://upload-images.jianshu.io/upload_images/10024246-227b1b4a5de0a7ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在美国本土，移动端图像下载数据量（KB）

有意思的是，使用 NuxtJS 和 Vue 的网站所下载的图像数据总量明显高于 React 网站。尽管如此，NuxtJS 仍能保持相当优秀的 LCP 良好比率。

Gatsby 和 NextJS 的下载图像数据量也不低，性能同样也不错，这意味着 Gatsby 出色的良好 LCP 比率绝不单靠削减图像尺寸。对我来说，这表明 Gatsby 可能会更早开始下载最大的图像，以优先处理的方式有序加载各项页面资源。

有趣的是，Gatsby 主页使用 WebP 处理图像，而 NextJS 主页使用的则是 AVIF。

## 总结

我们在本文中分析的所有框架，其良好性能比率均高于 0%，意味着它们都有构建高性能站点、拿下三绿 CWV 指标的能力。同样，所有这些框架的三绿比例也都达不到 100%，就是说如果使用不当，它们也都有可能会使网站变得加载缓慢，体验糟糕。

也就是说，我们看到的各种框架只在良好性能率方面存在差异。如果使用成绩较好的框架，那你的网站也许比其他框架开发的网站更有可能获得高性能。当然，大家可以在设计过程中根据具体情况再做考量。

另一方面，我们也看到这种差异有可能产生误导。例如，乍看之下 React 的性能似乎优于 Vue。但当我们排除掉大多数被包含在 React 统计中的 Wix 网站，转而关注高流量顶级网站时，Vue 的性能又迅速追平了 React。

此外，Preact 和 Svelte 等以性能而闻名的框架并不一定在 CWV 指标上有什么优势。不知道后续谷歌将 FID 替换成新的响应性指标后，这些框架能不能变得“名副其实”。

更让人意外的是，某些 Web 应用程序框架就算内置了 SSG/SSR 支持并使用了 CDN，性能上也还是没什么优势。换句话说，使用 Web 应用程序框架并不能有效提升实际性能。

另外，NextJS 和 NuxtJS 在提升网站 CWV 指标方面还有很长的路要走。虽然二者的改善趋势都很明显，尤其是 NuxtJS，但仍明显低于 Gatsby 或者 Wix，甚至还不及所有网站的平均良好率。希望他们再接再厉，尽早完成自我突破。

>**原文链接：**
https://www.smashingmagazine.com/2022/05/google-crux-analysis-comparison-performance-javascript-frameworks/
