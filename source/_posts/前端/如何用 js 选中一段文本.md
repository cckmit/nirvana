---
title: 如何用 js 选中一段文本
date: 2022-01-11 16:28:03
category: javascript
---
文本选择，在我们日常前端开发中并不常见，通常我们只有在富文本编辑器、段落文本复制等场景才会使用到。 功能实现很简单，只需要了解了 **Range** 对象的基本用法即可，今天就让我们一起来了解一下 **Range** 对象。

# 首先，我们做一点点准备

新建一个 html 文件，写一点简单的 html 片段。我们故意加入一些 span 来让 dom 结构变得复杂一些。 在浏览器打开这个网页，打开开发者工具，切换到 console，然后选择“上班”两个字。 到此，我们的所有准备完毕。

现在，我们在 console 直接输入 js 代码，边调试边看效果。 

# 第一步，如何拿到选中区域

```
window.getSelection()

```

我们得到了一个 **Selection** 对象。
![](https://upload-images.jianshu.io/upload_images/10024246-38ada7ef4b8b822e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


 让我们忽略掉那些很重要的细节，只看我们用得到的细节。 **Selection** 有一个原型方法 **getRangeAt**，它接受一个参数 **index**，表示选择第一个选区。 有小伙伴就要问了：难倒会有多个选区吗？ 还真有。在 Firefox 上，按住 ctrl 按键，是可以多选的。
![image.png](https://upload-images.jianshu.io/upload_images/10024246-9642c2fbf6326b89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 但是 Chrome 是不允许多选区的。 我们在 **Selection** 对象上继续调用 **getRangeAt**，得到第一个选区对象。

```
window.getSelection().getRangeAt(0)

```

![](https://upload-images.jianshu.io/upload_images/10024246-2855a88fc89d7f68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



 在上图的 **Range** 对象中，我们主要关注两对属性 **startContainer**、**startOffset** 和 **endContainer**、**endOffset**。
![](https://upload-images.jianshu.io/upload_images/10024246-527753013f9ae003.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 上图为我们的 DOM 结构，将其转化成框线图，方便分析。
 ![](https://upload-images.jianshu.io/upload_images/10024246-0c6663be69b78784.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 当我们选中“上班”两个字的时候，我们的 **start** 和 **end** 在上图中两条黑线所在位置。 此时，**startContaner** 和 **endContainer** 指的是 **span_1** 内的 **#text(上班)** 文本节点，文字“上”的左侧位置，在 **#text(上班)** 文本节点内 **index = 0** 的位置，所以 **startOffset = 0**，同理，**endOffset = 2**。

是不是有点蒙？我们换一个场景重新分析一下。我们选中“天不上”这三个字，再次获取一次 **Range** 对象。 
![](https://upload-images.jianshu.io/upload_images/10024246-42f312784cc82051.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


 可以看到，此时的 **startOffset** 和 **endOffset** 都是 1，然后此时起止光标并不在同一个位置。
![](https://upload-images.jianshu.io/upload_images/10024246-5066551ec1980946.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 从框线图我们看出，start 位置在 h1 内第一个的文本节点“明天”两个字中间，end 位置在 span_1 内“上班”两个字的中间。 所以此时的 **startContainer = #text(明天)**，**endContainer = #text(上班)**。 start 这条线相对于 **#text(明天)** 的索引位置是 1，因此 **startOffset = 1**，同理 **endOffset = 1**。

至此，我们从获取选区信息基本已经了解了

# 下一步，我们尝试通过 js 来更新选区

![](https://upload-images.jianshu.io/upload_images/10024246-be8d370fcda801a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 从 console 可以看出，**Range** 对象有 **setEnd** 方法，它接收 node 和 offset 两个参数，也就是设置框线图内 end 所在位置。同理，也有类似的 **setStart** 方法来设置选区的 start 位置。 我们如果想要选中“不上班”三个字，我们可将 start 和 end 的节点都设置为最外层包裹元素 h1，如下图所示。 
![](https://upload-images.jianshu.io/upload_images/10024246-0a84b729dba1ef8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 那么此时 **startOffset** 和 **endOffset** 应该是多少呢？ h1 元素，准确地说它包含了 5 个子节点，分别是：**#text(明天)、span_0(不)、span_1(上班)、#text(，)、span_2(放假)**。 我们可以直接把 start 放置到 **#text(明天)** 和 **span_0(不)** 中间，**span_0** 是 h1 第 2 个子元素，那么它前面的空位 index = 1，因此 **startOffset = 1。** 同样，end 放置到 **span_1(上班)** 和 **#text(，)** 中间，**endOffset = 3**。

```
const range = window.getSelection().getRangeAt(0)
const h1 = document.getElementsByTagName('h1')[0]
range.setStart(h1, 1)
range.setEnd(h1, 3)
```

![](https://upload-images.jianshu.io/upload_images/10024246-7ee6753613a78f7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 我们可以看到，“不上班” 3 个字被顺利选中了。

# 如何凭空选中

上面所有操作的前提是我们已经选中了一段文本区域，如果我们没有进行任何选中操作，执行 **getRangeAt(0)** 会报错。
![](https://upload-images.jianshu.io/upload_images/10024246-e61715bab0f324a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 此时，我们应该先通过 **Selection** 对象的 **rangeCount** 判断是否有选取，如果没有，可以调用 **Selection.addRange** 方法，新建一个选区。**addRange** 方法接收一个 **Range** 对象作为参数，我们可以通过 **document.createRange** 方法创建空选区，再把返回的 **newRange** 传给 **addRange** 方法即可。

```
const newRange = document.createRange()
newRange.setStart(window.span_1.childNodes[0], 1)
newRange.setEnd(window.span_2.childNodes[0], 1)
window.getSelection().addRange(newRange)
```

![](https://upload-images.jianshu.io/upload_images/10024246-642f4e40d8bb3c66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 这样就选中了“班，放”三个字，示意图如下。
![](https://upload-images.jianshu.io/upload_images/10024246-b0560cfc9621bb1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 其他应用

**Range** 对象的能力不仅如此，如果我们设置 start 和 end 为同一位置，那其实就等同于将光标移动到某个位置。 除此之外，还能进行节点的添加 **insertNode** 和删除 **deleteContents**，因此 **Range** 在富文本编辑器的开发中举足轻重。

 富文本编辑器的实现，今后有机会再和大家详细介绍，祝大家元旦节快乐。

# 参考

[developer.mozilla.org/zh-CN/docs/…](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FSelection "https://developer.mozilla.org/zh-CN/docs/Web/API/Selection") [developer.mozilla.org/zh-CN/docs/…](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FRange "https://developer.mozilla.org/zh-CN/docs/Web/API/Range")

>作者：CreditFE信用前端
链接：https://juejin.cn/post/7047022519294885924
