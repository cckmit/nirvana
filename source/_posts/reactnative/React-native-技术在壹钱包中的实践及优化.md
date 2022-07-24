---
title: React-native-技术在壹钱包中的实践及优化
date: 2021-03-10 21:12:40
categories: react-native
---
![](https://upload-images.jianshu.io/upload_images/10024246-5cee99438e7340ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-cb00304de33dc6a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-0be45ffec5a24d23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-438492d08e3f2b1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

REACT-NATIVE作为前端框架，到现在大概有三年的时间。我这边先分享一下为什么使用REACT-NATIVE。

首先REACT-NATIVE的开发效率，使用javaScript开发原生应用，提出Learn Once, Write Anywhere
的开发思想。兼容前端的最大优势灵活发布，可以支持线上业务功快速迭代和随时更新发布。又保证了用户体验，整体体验接近原生，远优于Hybrld和Web APP。同时整个框架保持很高的社区热度，自身框架不断的升级优化，有丰富的第三方组件。

![](https://upload-images.jianshu.io/upload_images/10024246-9e2b86157dc6a78a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们一开始接触RN是2015年，当时RN刚刚发布，在2015年12月基于0.17版本开发了同时兼容iOS和Android的Demo，整体体验还可以；同时我们一直在关注RN的发展，在2016年9月使用RN0.38版本开发收银台业务，12月份正式上线运行，同时实现了包括bundle拆分，热更新的技术。整体运行了3个月的事件，产品和运营对该技术的体验比较满意和认可，就在公司推进该框架的接入；2017年3月，商城业务接入，5月正式支撑公司大型活动，技术上实现了RN单实例，单文件diff；在2017年我们整个商城业务的主流程全部接入了RN，2018年又接入了理财商城业务。

![](https://upload-images.jianshu.io/upload_images/10024246-9beeb7a89b7a3c43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

壹钱包的应用场景，我们有搜索分类，还有详情、会场、收银台、理财，后面就是我们正在做的5.20的活动，页面活动有很多的，商城里面很多应用，当时统计了一下，一个商品会场里面图片大概600多张，如果是RN原生的技术不优化的话，是无法支持这么大的应用场景，对于REACT-NATIVE我们做了性能优化。

![](https://upload-images.jianshu.io/upload_images/10024246-8973f3d7940d9de9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-40029c5e215d9329.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-c3cdf376c6f745a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

关注过RN的人知道，其实REACT-NATIVE对于安卓的支持较晚，安卓的性能不是很好，所以我们在安卓上进行较深入的优化。
RN是通过reactprop和react method两个注解声明暴露给js的属性和方法的，通过react-native init 的工程中rn框架并未进行优化，所有注解的处理是在运行时进行，这是一个非常耗时的过程。此外，通过反射的形式修改属性和调用方法也是不高效的。我们基于rn开源项目，编译出自己的rn库，完成了对注解的编译时优化。rn开源代码中有react prop注解的处理器，只需要开启就行。右边这幅图就是编译过程中生成的优化代码。针对react method注解的优化思路也是类似，我们自己开发出一套注解处理器，并修改rn源码，使优化生效。右边这幅图展示的就是编译阶段生成的优化代码。通过注解的编译时优化，rn框架初始化过程中的关键方法处理速度提升了60倍。

![](https://upload-images.jianshu.io/upload_images/10024246-9f76b114a1aeeabc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

除了注解优化，我们还对rn框架进行了诸多优化。
这些措施包括了比如我们在iOS方面的RCTImage改造，支持共享客户端缓存，支持动态注册RN模块，清除日志相关影响，无操作时，尽量不去执行刷新操作。

![](https://upload-images.jianshu.io/upload_images/10024246-6437a0f4b2e9fed1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

RN主要性能瓶颈在Javascript的执行 ，JS也是非常耗时的。RN框架代码是整体比较大，压缩之后大概500多K，如果多个业务接入RN的话，每个业务都打包框架代码时，造成很多资源浪费。业务开发并不关心当前框架是什么版本，只负责业务开发的，我们把框架代码独立更加方便于其他业务进行接入，方便业务进行开发。
我们做了JS bundle拆分，分为是框架JS和业务JS，框架JS通过global.react=require（react）,global.reactNative=require（react-native）把react和react-native对象定义为全局变量;业务JS通过webpack替换代码中的react和react-native对象为全局对象，实现RN框架的引入。
Bundle拆分后，在执行时还是要合并加载的，单我们不是简单的把框架JS和业务JS直接合并，那样拆分就没有太多意义。我们这边提出了一个新的方案，只加载一个框架JS，因为所有的业务JS执行都是基于框架执行的，加载一个RN框架就可以支撑所有的业务，你不需要再去对每个业务执行框架；对于单独加载框架JS，我们做过统计，哪怕只加载RN框架的话，其实耗时也是很严重的；所以我们加载业务JS都是基于当前的框架去执行的。通过这样的方式我们实现了RN的单实例共享。

![](https://upload-images.jianshu.io/upload_images/10024246-276644512e726e96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其他方面，我们去JS加载做了一些其他的优化，比如JS moudle加载之后，我们会把它放到这里面，不会重复加载，当有相同业务打开的时候，我们去执行缓存中的JS代码。APP启动的时候，我们预加载RN实例，我们在打开APP的时候，我们先把框架执行，当打开RN业务时，我们这个时候不需要再去执行整个框架，我们直接执行业务代码，这样就会大大提升业务整体的性能，我们对于业务JS进行智能预加载。

![](https://upload-images.jianshu.io/upload_images/10024246-6514e5443290fb01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们在商品详情页启动耗时，有一半的业务场景在400毫秒到300毫秒打开，这个能够保证我们的页面完成最基本的要求就是秒开的场景。

![](https://upload-images.jianshu.io/upload_images/10024246-2c81b8e6aa60111a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-5e86df631134e037.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

RN一个优势就是支持灵活发布，我们能对业务进行热更新，我们有一个专门管理和发布的平台。我们RN开发统一在git上开发，所有业务放在Git仓库；为了RN打包，我们基于Node开发了自动化打包平台，当需要发布业务的时候，我们通过自动化打包平台，自动化去打包生成业务和框架的zip包，上传到我们的发布系统，发布系统根据新的zip包，进行BSDIFF算法生成增量更新，这样用户更新不需要现在这个新业务zip包，只需要下载diff增量内容，
最大限度的降低用户流量，统计下来增量升级流量降低90%以上。这个时候如果我们对应的业务需要打开对应的RN的业务的时候，我们先去读取配置，这个决定你当前的版本是否支持RN，然后打开业务。

![](https://upload-images.jianshu.io/upload_images/10024246-0088928e23b9583f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-97cb7ac0864cb890.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

RN支持使用Native组件，但是我们在开发中并不是全部依赖于native组件，自定义组件开发有对应的原则，优先使用Native已有的UI组件，如自定义键盘、密码。减少Native与JS间的交互，如倒计时。JS实现效果不佳或者实现困难，如滚轮选择器（picker）。现有组件无法支持当前业务需求，如列表组件（TableView）。

![](https://upload-images.jianshu.io/upload_images/10024246-f1249558ac8b1088.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

RN提供的listview几乎无复用和flatview是基于原生组件的滚动事件进行复用，这样会有一定的延时，原生列表组件在复用上更加高效，所以基于原生组件上，开发了自己的RN列表组件；如图是我们在优化TableView的修改思路。优化过后有非常大的提升。

![](https://upload-images.jianshu.io/upload_images/10024246-ba16b2305c7a8815.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

大家看一下这是我们现在REACT-NATIVE实现的业务场景。绿色的就是我们经常看到的所谓的60帧的刷新频率线，现在的页面基本上正常滚动的话，都是在60帧，整体体验是非常接近原生的。

![](https://upload-images.jianshu.io/upload_images/10024246-b5cefe1b5284ec2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-a82a765cd5d68a45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

RN包括JS，包括Native都需要对异常进行捕获，因为RN异常会造成App的crush，这样对于整体业务体验非常不理想，我们做了对应的捕获方案。

我们分为两块，一个就是JS异常，还有就是Native异常。JS异常我们使用RN提供的使用ErrorUtils全局捕获异常。Native异常，Android通过SetNativeModuleCallEXceptionHandler捕获；iOS通过捕获RCTCurrentFatalHandler的红屏错误，并监听RCTJavaScriptDidFailToLoadNotification 的异常通知，另外在打开业务时会检测RCTRootView，如果5秒内未加载ContentView也会认为异常。 捕获到异常我们会记录异常灰度，并对业务进行降级跳转到H5页面

![](https://upload-images.jianshu.io/upload_images/10024246-da7b7fd0ff3d3957.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

RN框架版本升级，RN开发是2017年开始进行，版本为0382，RN框架升级迭代是非常快的，现在已经到54了；RN的框架升级过程中，框架自己也在解决自己的Bug，性能也是提高很多，稳定性也提升了。Native端框架整体性能、稳定性提升，其中Android布局方式发生了很大的变化
；React.js版本由15升级到16，在异常处理，服务端渲染和核心架构方面（引入 Fiber）都有了重大更新。基于此我们要升级我们对应的RN框架，跟上RN整体框架的版本步伐。

![](https://upload-images.jianshu.io/upload_images/10024246-9a4ecffbaf5d6cd6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面是RN的框架，上面是我们对应的业务，当我们选择去升级RN的时候，这个时候出现了比较严重的问题，由于它的版本差别太大了，整体业务出现很大问题，38版本下的业务代码无法在52版本运行，52版本的业务代码也无法在38版本上运行；开发那边讨论的时候，同时开展两条业务也不太现实，产品也不同意放弃旧的框架业务，基于此，我们做了一个框架兼容层，去屏蔽掉38和52的框架差异，实现基于一套业务代码能够同时在38和52上面同时运行，对于业务来讲，不需要关注框架的一些差异，只是按照对应的业务开发业务代码。

![](https://upload-images.jianshu.io/upload_images/10024246-663c02b507ddb0ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在升级的时候我们查看了了React和React-native的变动日志。React升级变动，废弃React createClass，使用独立包，create-react-class代替。独立React.PropTypes，使用新包prop-types，React-native中使用ViewPropTypes替换View.propTypes 。iOS修改了Native的事件发送机制，由EventDispatcher改为 EventBlock。

![](https://upload-images.jianshu.io/upload_images/10024246-2ea6778d0374176f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

大家可以看到RN框架升级之后，52相对38整体业务有很大的提升，我们现在刚才看到的一个图表，50%的业务我们能够在200毫秒内打开，70%的业务能够在300-400毫秒内，整体业务提升相对以前的38框架大概提升了50%的效果。

![](https://upload-images.jianshu.io/upload_images/10024246-9717988db8365892.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-c5cb55c11a3e0fbb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

RN未来的展望，在APP内部业务RN的逐步替换；JS异常定位；RN单页化，现有业务都是通过native打开，造成数据无法共享，通过单页化，业务通过前端自己打开业务；现在RN是Learn once，Write anywhere，现有业务要求我们即存在RN又有对应的H5场景，同一个需求需要开发2遍，开发压力较大，下期目标实现Write once，run anywhere，完成同一套代码支持Web和RN；最后就是我们的对外输出RN的自定义组件和解决方案。
