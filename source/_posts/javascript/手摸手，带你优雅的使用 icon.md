---
title: 手摸手，带你优雅的使用 icon
date: 2022-04-02 16:28:03
categories: 前端
---
## 演进史

首先我们来说一下前端 icon 的发展史。

**远古时代** 在我刚开始实习时，大部分图标都是用 img 来实现的。渐渐发现一个页面的请求资源中图片 img 占了大部分，所以为了优化有了`image sprite` 就是所谓的雪碧图，就是将多个图片合成一个图片，然后利用 css 的 background-position 定位显示不同的 icon 图标。但这个也有一个很大的痛点，维护困难。每新增一个图标，都需要改动原始图片，还可能不小心出错影响到前面定位好的图片，而且一修改雪碧图，图片缓存就失效了，久而久之你不知道该怎么维护了。
![](https://upload-images.jianshu.io/upload_images/10024246-b28e2473377baa3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**font 库** 后来渐渐地一个项目里几乎不会使用任何本地的图片了，而使用一些 font 库来实现页面图标。常见的如 [Font Awesome](http://fontawesome.io/) ，使用起来也非常的方便，但它有一个致命的缺点就是找起来真的很不方便，每次找一个图标特别的费眼睛，还有就是它的定制性也非常的不友善，它的图标库一共有675个图标，说少也不少，但还是会常常出现找不到你所需要图标的情况。当然对于没有啥特别 ui 追求的初创公司来说还是能忍一忍的。但随着公司的壮大，来了越来越多对前端指手画脚的人，丧心病狂的设计师，他们会说不！这icon这么丑，这简直是在侮辱他们高级设计师的称号啊！不过好在这时候有了[iconfont](http://iconfont.cn/) 。

**iconfont** 一个阿里爸爸做的开源图库，人家还有专门的 [github issue](https://github.com/thx/iconfont-plus/issues)(虽然我的一个 issue 半年多了也没回应/(ㄒoㄒ)/~~)，但人家的图标数量还是很惊人的，不仅有几百个公司的开源图标库，还有各式各样的小图标，还支持自定义创建图标库，所以不管你是一家创业公司还是对设计很有要求的公司，它都能很好的帮助你解决管理图标的痛点。你想要的基本都有~

## iconfont 三种使用姿势

### unicode

最开始我们使用了`unicode`的格式，它主要的特点是 **优势**

*   兼容性最好，支持ie6+
*   支持按字体的方式去动态调整图标大小，颜色等等

**劣势**

*   不支持多色图标
*   在不同的设备浏览器字体的渲染会略有差别，在不同的浏览器或系统中对文字的渲染不同，其显示的位置和大小可能会受到font-size、line-height、word-spacing等CSS属性的影响，而且这种影响调整起来较为困难

**使用方法：** 第一步：引入自定义字体 `font-face

```
 @font-face {
   font-family: "iconfont";
   src: url('iconfont.eot'); /* IE9*/
   src: url('iconfont.eot#iefix') format('embedded-opentype'), /* IE6-IE8 */
   url('iconfont.woff') format('woff'), /* chrome, firefox */
   url('iconfont.ttf') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+*/
   url('iconfont.svg#iconfont') format('svg'); /* iOS 4.1- */
 }
```

第二步：定义使用iconfont的样式

```
.iconfont {
  font-family:"iconfont" !important;
  font-size:16px;
  font-style:normal;
  -webkit-font-smoothing: antialiased;
  -webkit-text-stroke-width: 0.2px;
  -moz-osx-font-smoothing: grayscale;
}
```

第三步：挑选相应图标并获取字体编码，应用于页面

```
<i class="iconfont">&#xe604;</i>
```

效果图：
![](https://upload-images.jianshu.io/upload_images/10024246-aab55288e998951d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


其实它的原理也很简单，就是通过 `@font-face` 引入自定义字体(其实就是一个字体库)，它里面规定了`&#xe604` 这个对应的形状就长这企鹅样。其实类似于 '花裤衩'，在不同字体设定下长得是不同的一样。
![](https://upload-images.jianshu.io/upload_images/10024246-b299659b036728a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


不过它的缺点也显而易见，`unicode`的书写不直观，语意不明确。光看`&#xe604;`这个`unicode`你完全不知道它代表的是什么意思。这时候就有了 `font-class`。

### font-class

与unicode使用方式相比，具有如下特点：

*   兼容性良好，支持ie8+
*   相比于unicode语意明确，书写更直观。可以很容易分辨这个icon是什么。

**使用方法：** 第一步：拷贝项目下面生成的fontclass代码：

```
../font_8d5l8fzk5b87iudi.css
```

第二步：挑选相应图标并获取类名，应用于页面：

```
<i class="iconfont icon-xxx"></i>
```

效果图：
![](https://upload-images.jianshu.io/upload_images/10024246-e3e91778ced38da6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
它的主要原理其实是和 `unicode` 一样的，它只是多做了一步，将原先`&#xe604`这种写法换成了`.icon-QQ`，它在每个 class 的 before 属性中写了`unicode`,省去了人为写的麻烦。如 `.icon-QQ:before { content: "\e604"; }`

相对于`unicode` 它的修改更加的方便与直观。但也有一个大坑，之前楼主一个项目中用到了两组`font-class` 由于没有做好命名空间，所有的class都是放在`.iconfont` 命名空间下的，一上线引发了各种雪崩问题，修改了半天，所以使用`font-class`一定要注意命名空间的问题。

### symbol

随着万恶的某某浏览器逐渐淡出历史舞台，svg-icon 使用形式慢慢成为主流和推荐的方法。相关文章可以参考张鑫旭大大的文章[未来必热：SVG Sprite技术介绍](http://www.zhangxinxu.com/wordpress/2014/07/introduce-svg-sprite-technology/?spm=a313x.7781069.1998910419.50)

*   支持多色图标了，不再受单色限制。
*   支持像字体那样通过font-size,color来调整样式。
*   支持 ie9+
*   可利用CSS实现动画。
*   减少HTTP请求。
*   矢量，缩放不失真
*   可以很精细的控制SVG图标的每一部分

**使用方法：** 第一步：拷贝项目下面生成的symbol代码：

```
引入  ./iconfont.js
```

第二步：加入通用css代码（引入一次就行）：

```
<style type="text/css">
    .icon {
       width: 1em; height: 1em;
       vertical-align: -0.15em;
       fill: currentColor;
       overflow: hidden;
    }
</style>
```

第三步：挑选相应图标并获取类名，应用于页面：

```
<svg class="icon" aria-hidden="true">
    <use xlink:href="#icon-xxx"></use>
</svg>
```

使用svg-icon的好处是我再也不用发送`woff|eot|ttf|` 这些很多个字体库请求了，我所有的svg都可以内联在html内。

![](https://upload-images.jianshu.io/upload_images/10024246-4940604f4757d368.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还有一个就是 svg 是一个真正的矢量，不管你再怎么的放缩它都不会失真模糊，而且svg可以控制的属性也更加的丰富，也能做出更加生动和复杂的图标。现在ui设计师平时都喜欢使用 sketch 来工作，只要轻松一键就能导出 svg 了，所以 svg 也更受设计师的青睐。[Inline SVG vs Icon Fonts](https://css-tricks.com/icon-fonts-vs-svg/) 这篇文章详细的比较了 `svg` 和 `icon-font`的优劣，大家可以去看看。PS：这里其实还用到了 `SVG Sprite` 技术。简单的理解就是类 svg 的似雪碧图，它在一个 svg 之中运用 symbol 标示了一个一个的 svg 图标，这样一个页面中我们遇到同样的 svg 就不用重复再画一个了，直接使用`<use xlink:href="#icon-QQ" x="50" y="50" />` 就能使用了，具体的细节可以看这篇文章开头的文章 [未来必热：SVG Sprite技术介绍](http://www.zhangxinxu.com/wordpress/2014/07/introduce-svg-sprite-technology/)，在之后的文章中也会手摸手叫你自己如何制作 `SVG Sprite`。

## 创建 icon-component 组件

我们有了图标，接下来就是如何在自己的项目中优雅的使用它了。 之后的代码都是基于 vue 的实例(ps: react 也很简单，原理都是类似的)

```
//components/Icon-svg
<template>
  <svg class="svg-icon" aria-hidden="true">
    <use :xlink:href="iconName"></use>
  </svg>
</template>

<script>
export default {
  name: 'icon-svg',
  props: {
    iconClass: {
      type: String,
      required: true
    }
  },
  computed: {
    iconName() {
      return `#icon-${this.iconClass}`
    }
  }
}
</script>

<style>
.svg-icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>

复制代码
```

```
//引入svg组件
import IconSvg from '@/components/IconSvg'

//全局注册icon-svg
Vue.component('icon-svg', IconSvg)

//在代码中使用
<icon-svg icon-class="password" />
```

就这样简单封装了一个 `Icon-svg` 组件 ，我们就可以简单优雅的在自己的vue项目之中使用图标了。

## 进一步改造

但作为一个有逼格的前端开发，怎能就此满足呢!目前还是有一个致命的缺点的，就是现在所有的 `svg-sprite` 都是通过 iconfont 的 `iconfont.js` 生成的。

*   首先它是一段用js来生成svg的代码，所有图标 icon 都很**不直观**。

![](https://upload-images.jianshu.io/upload_images/10024246-04c41d4a1b21b098.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你完全不知道哪个图标名对应什么图标，一脸尼克扬问号??? 每次增删改图标只能整体js文件一起替换。

*   其次它也做不到**按需加载**，不能根据我们使用了那些 svg 动态的生成 `svg-sprite`。
*   **自定义性差**，通常导出的svg包含大量的无用信息，例如编辑器源信息、注释等。通常包含其它一些不会影响渲染结果或可以移除的内容。
*   **添加不友善**，如果我有一些自定义的svg图标，该如何和原有的 `iconfont` 整合到一起呢？目前只能将其也上传到 `iconfont` 和原有的图标放在一个项目库中，之后再重新下载，很繁琐。

### 使用 svg-sprite

接下来我们就要自己来制作 `svg-sprite` 了。这里要使用到 [svg-sprite-loader](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fkisenka%2Fsvg-sprite-loader "https://github.com/kisenka/svg-sprite-loader") 这个神器了， 它是一个 webpack loader ，可以将多个 svg 打包成 `svg-sprite` 。

我们来介绍如何在 `vue-cli` 的基础上进行改造，加入 `svg-sprite-loader`。

我们发现`vue-cli`默认情况下会使用 `url-loader` 对svg进行处理，会将它放在`/img` 目录下，所以这时候我们引入`svg-sprite-loader` 会引发一些冲突。

```
//默认`vue-cli` 对svg做的处理，正则匹配后缀名为.svg的文件，匹配成功之后使用 url-loader 进行处理。
 {
    test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
    loader: 'url-loader',
    options: {
      limit: 10000,
      name: utils.assetsPath('img/[name].[hash:7].[ext]')
    }
}
```

解决方案有两种，最简单的就是你可以将 test 的 svg 去掉，这样就不会对svg做处理了，当然这样做是很不友善的。

*   你不能保证你所有的 svg 都是用来当做 icon的，有些真的可能只是用来当做图片资源的。
*   不能确保你使用的一些第三方类库会使用到 svg。

所以最安全合理的做法是使用 webpack 的 [exclude](https://webpack.js.org/configuration/module/#rule-exclude) 和 [include](https://webpack.js.org/configuration/module/#rule-include) ，让`svg-sprite-loader`只处理你指定文件夹下面的 svg，`url-loaer`只处理除此文件夹之外的所以 svg，这样就完美解决了之前冲突的问题。 代码如下

![](https://upload-images.jianshu.io/upload_images/10024246-57235d8c6c896655.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样配置好了，只要引入svg之后填写类名就可以了

```
import '@/src/icons/qq.svg; //引入图标

<svg><use xlink:href="#qq" /></svg>  //使用图标

```

单这样还是非常的不优雅，如果我项目中有一百个 icon，难不成我要手动一个个引入么！ **偷懒是程序员的第一生产力！！！**

## 自动导入

首先我们创建一个专门放置图标 icon 的文件夹如：`@/src/icons`，将所有 icon 放在这个文件夹下。 之后我们就要使用到 webpack 的 [require.context](https://webpack.js.org/guides/dependency-management/#require-context)。很多人对于 `require.context`可能比较陌生，直白的解释就是

> require.context("./test", false, /.test.js$/); 这行代码就会去 test 文件夹（不包含子目录）下面的找所有文件名以 `.test.js` 结尾的文件能被 require 的文件。 更直白的说就是 我们可以通过正则匹配引入相应的文件模块。

require.context有三个参数：

*   directory：说明需要检索的目录
*   useSubdirectories：是否检索子目录
*   regExp: 匹配文件的正则表达式

了解这些之后，我们就可以这样写来自动引入 `@/src/icons` 下面所有的图标了

```
const requireAll = requireContext => requireContext.keys().map(requireContext)
const req = require.context('./svg', false, /\.svg$/)
requireAll(req)
```

之后我们增删改图标直接直接文件夹下对应的图标就好了，什么都不用管，就会自动生成 `svg symbol`了。

![](https://upload-images.jianshu.io/upload_images/10024246-9678298b3fda796b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 更进一步优化自己的svg

首先我们来看一下 从 `阿里iconfont` 网站上导出的 svg 长什么样？

![](https://upload-images.jianshu.io/upload_images/10024246-6f30dc49d3d38659.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

没错虽然 iconfont 网站导出的 svg 内容已经算蛮精简的了，但你会发现其实还是与很多无用的信息，造成了不必要的冗余。就连 iconfont 网站导出的 svg 都这样，更不用说那些更在意 ui漂不漂亮不懂技术的设计师了(可能)导出的svg了。好在 `svg-sprite-loader`也考虑到了这点，它目前只会获取 svg 中 path 的内容，而其它的信息一概不会获取。生成 svg 如下图：
![](https://upload-images.jianshu.io/upload_images/10024246-c908a48dce829959.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但任何你在 path 中产生的冗余信息它就不会做处理了。如注释什么的
![](https://upload-images.jianshu.io/upload_images/10024246-f20d83fbb11c2cc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这时候我们就要使用另一个很好用的东西了-- [svgo](https://github.com/svg/svgo)

> SVG files, especially exported from various editors, usually contain a lot of redundant and useless information such as editor metadata, comments, hidden elements, default or non-optimal values and other stuff that can be safely removed or converted without affecting SVG rendering result.

它支持几十种优化项，非常的强大，8k+的star 也足以说明了问题。

详细的操作可以参照 [`官方文档`](https://github.com/svg/svgo) [`张鑫旭大大的文章`](http://www.zhangxinxu.com/wordpress/2016/02/svg-compress-tool-svgo-experience/)（没错又是这位大大的文章，或许这就是大佬吧！）本文就不展开了。

## 写在最后

上面大概阐述了一下前端项目中 icon 使用的演进史。 总的来说还是那句话，**适合的才是最好的**。就拿之前争论的选择 vue react 还是 angular，个人觉得每个框架都有自己的特点和适用的业务场景，所以所有不结合业务场景的推荐和讨论都是瞎bb。。。如上文其实大概讲了五种前端icon的使用场景，第一种`Font Awesome`不用它并不是因为它不好，而是业务场景不适合，如果你团队没有专门的设计师或者对 icon 的自定义度不高完全可以使用它，[Font Awesome](https://github.com/FortAwesome/Font-Awesome) github有五万多 star，足见社区对它的认可。还比如说，你们项目对低端浏览器有较高的适配要求，你还强行要用 svg 作为图标 icon，那你真的是存心和自己过不去了。所以所有方案都没有绝对的优与劣之分，适合自己业务场景，解决自己实际痛点，提高自己开发效率的方案就是好的方案。

>作者：花裤衩
链接：https://juejin.cn/post/6844903517564436493
