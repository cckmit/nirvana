---
title: FP-RP-FRP编程范式
date: 2021-03-11 21:12:40
categories: 前端
---

![](https://upload-images.jianshu.io/upload_images/10024246-e551555c2919e46c.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-837712d823c88c44.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-7ab8e5e5c0fa23fa.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-820693fcfaded8bc.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-0dd0666c417ff235.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>数据和视图的分离->解耦（开放封闭原则）jq的数据与视图混在一块（意大利面条式代码），Vue的数据与视图分离 以数据驱动视图（只关心数据变化，DOM操作被封装）jq直接用js修改视图，Vue以数据驱动视图

![](https://upload-images.jianshu.io/upload_images/10024246-42d5fd6651b5c730.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](https://upload-images.jianshu.io/upload_images/10024246-c4476563b9f416e1.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-61c4c317ab6a1925.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-1b0d1dad4068ccde.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-488439027be1a632.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-11073c21bc77376b.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-cbb9a98c4e60d8ec.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10024246-cb1449cf1705be2c.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>数据不可变： 它要求你所有的数据都是不可变的，这意味着如果你想修改一个对象，那你应该创建一个新的对象用来修改，而不是修改已有的对象。 无状态： 主要是强调对于一个函数，不管你何时运行，它都应该像第一次运行一样，给定相同的输入，给出相同的输出，完全不依赖外部状态的变化。 为了实现这个目标，函数式编程提出函数应该具备的特性：没有副作用和纯函数。


![](https://upload-images.jianshu.io/upload_images/10024246-7fe37c43559a2198.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-bfbb62895921c27e.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>在不纯的版本中，checkAge 的结果将取决于 minimum 这个可变变量的值。换句话说，它取决于系统状态（system state）；，因为它引入了外部的环境，从而增加了认知负荷。但这种依赖状态是影响系统复杂度的罪魁祸首，输入值之外的因素能够左右 checkAge 的返回值，不仅让它变得不纯，而且导致每次我们思考整个软件的时候都痛苦不堪。 另一方面，使用纯函数的形式，函数就能做到自给自足。

![](https://upload-images.jianshu.io/upload_images/10024246-44dafec478425db0.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>保证函数没有副作用，一来能保证数据的不可变性，二来能避免很多因为共享状态带来的问题。当你一个人维护代码时候可能还不明显，但随着项目的迭代，项目参与人数增加，大家对同一变量的依赖和引用越来越多，这种问题会越来越严重。最终可能连维护者自己都不清楚变量到底是在哪里被改变而产生Bug。`传递引用一时爽，代码重构火葬场`  

![](https://upload-images.jianshu.io/upload_images/10024246-e4daf335f0fe4716.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-15e68c71c8faa114.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-c933554bb5a211a3.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>为什么这个单元函数很重要？还记得我们之前说过的，函数的返回值，有且只有一个嘛？ 如果我们想顺利的组装流水线，那我就必须保证我每个加工站的输出刚好能流向下个工作站的输入。因此，在流水线上的加工站必须都是单元函数。

![](https://upload-images.jianshu.io/upload_images/10024246-f0ab24af87d1c69a.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-01a3212c7593560d.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-e5e473d34cd15743.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-4fdfb5310ffc3c0c.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>函数组合的好处显而易见，它让代码变得简单而富有可读性，同时通过不同的组合方式，我们可以轻易组合出其他常用函数，让我们的代码更具表现力

![](https://upload-images.jianshu.io/upload_images/10024246-c947c9ccd7dccd73.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-d1f499f381e2ff6c.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>函数式编程很理想，但是我们还是要与外部数据进行交互，不然我们的代码就没有意义当一套复杂的数据响应被应用于一套复杂的应用中时，开发者往往在响应来响应去的连环嵌套响应中被搞昏头，因为数据的响应而牵一发动全身，导致整个响应很难控制，这是数据响应必须解决的问题。

![](https://upload-images.jianshu.io/upload_images/10024246-dee5d9f145d85c79.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-c027a0b749c44425.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-1badbd997d2e324e.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
