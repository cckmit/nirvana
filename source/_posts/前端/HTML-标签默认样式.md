---
title: HTML-标签默认样式
date: 2021-03-11 21:12:40
category: javascript
---
【基础】

**<div>** 定义文档中的节（块元素，无任何属性）

**<span>** 定义文档中的节（内联元素，无任何属性）

**<h1>** to **<h6>** 定义标题（通常使用粗体显示。注意：单个页面内最好只使用1个H1标签）

H1 标题

H2 标题

H3 标题

H4 标题

H5 标题

H6 标题

**<p>** 定义段落

**<hr>** 定义水平线（本页中的分割线均为 <hr> 标签）

**<header>** 定义 section 或页面的头部

**<footer>** 定义 section 或页面的尾部

**<article>** 定义文章

**<section>** 定义文档中的节

**<aside>** 定义文档内容相关的内容

**<nav>** 定义导航

header 头部

footer 尾部

article 内容

section 内容

aside 内容

nav 内容

**<details>** 定义细节内容

**<summary>** 定义 details 的标题

HTML 5

【列表】

**<ul>** 定义无序列表（通常列表前会有项目符号）

**<li>** 定义列表的项目

张三

李四

**<ol>** 定义有序列表。（通常列表前会有数字符号）

**<li>** 定义列表的项目

张三

李四

**<dl>** 定义定义列表

**<dt>** 定义定义列表中的项目

**<dd>** 定义定义列表中项目的描述

张三的家谱

张三的家谱于1900年开始统计，进行了..

【表格】

**<table>** 定义表格

**<caption>** 定义表格标题

**<thead>** 定义表格中的表头内容

**<tfoot>** 定义表格中的表注内容（脚注）

**<tbody>** 定义表格中的主体内容

**<tr>** 定义表格中的行

**<th>** 定义表格中的表头单元格（通常使用粗体显示）

**<td>** 定义表格中的单元

table 结构标准顺序如下：

<table>

  <caption></caption>

  <thead>

    <tr>

      <th></th>

    </tr>

  </thead>

  <tfoot>

    <tr>

      <td></td>

    </tr>

  </tfoot>

  <tbody>

    <tr>

      <td></td>

    </tr>

  </tbody>

</table>

※ 虽然 tfoot 放在了 tbody 之前，浏览器依然会将 tfoot 显示在 tbody 之后，而且这样做能让浏览器在获得所有表格内的数据前显示表注。

表格标题

表头 ID表头 姓名表头 日期

表注 这是编号表注 这是假名表注 这是添加日期

1张三2009-09-09

2李四2010-10-10

自带边框样式

表头 ID表头 姓名表头 日期

表注 这是编号表注 这是假名表注 这是添加日期

1张三2009-09-09

2李四2010-10-10

【表单】

**<form>** 定义表单

**<fieldset>** 定义围绕表单中元素的边框（通常四周会有缩进，并显示围绕的边框）

**<legend>** 定义 fieldset 元素的标题

标题

内容

**<select>** 定义选择列表（下拉列表、多选列表）

**<optgroup>** 定义选择列表中相关选项的组合

**<option>** 定义选择列表中的选项

张三张三的儿子张三的孙女李四李四的女儿李四的孙子

滚动列表形式

张三张三的儿子张三的孙女李四李四的女儿李四的孙子

多选列表

张三张三的儿子张三的孙女李四李四的女儿李四的孙子

**<input>** 定义输入控件（如果浏览器不支持 HTML5 新的类型，那么会使用文本域替代）

文本域 type="text" 

密码域 type="password" 

复选框 type="checkbox" A B C

单选按钮 type="radio" 组A:① ②　　组B:Ⅰ Ⅱ

文件域 type="file" 

图像域 type="image" （可用做提交按钮）

隐藏域 type="hidden" （当然是看不见的了）

普通按钮 type="button" 

重置按钮 type="reset" 

提交按钮 type="submit" 

email 域 type="email" （若有输入内容，则会验证格式是否符合 email）

url 域 type="url" （若有输入内容，则会验证格式是否符合 url）

数值域 type="number" （若有设置最大值或最小值，则会验证数字是否在最大最小值之内）

数值范围域 type="range" （通过拖动滑块来选择数值）

日期域 type="date" （会调用浏览器自带的日期选择器，可设置的类型：date, month, week, time, datetime, datetime-local）

 type="month" 

 type="week" 

 type="time" 

 type="datetime" 

 type="datetime-local" 

色值域 type="color" （会调用浏览器自带的颜色选择器）

搜索域 type="search" （用于搜索，站内搜索或 Google 搜索等，在输入框内容右侧通常会出现清除按钮）

**<datalist>** 定义 input 元素的选项列表

**<keygen>** 定义生成秘钥

**<output>** 定义多行的文本输入控件

**<label>** 定义 input 元素的标注点击这里也可以选中

**<textarea>** 定义多行的文本输入控件

**<button>** 定义按钮（与 input 不同的是，button 内部可以放置更多的内容，比如文本或图像）

普通按钮 重置按钮 提交按钮

【格式】

**<blockquote>** 定义长的引用（通常四周会有缩进）

WEB标准不是某一个标准，而是一系列标准的集合。网页主要由三部分组成：结构（Structure）、表现（Presentation）和行为（Behavior）。对应的标准也分三方面：结构化标准语言主要包括XHTML和XML，表现标准语言主要包括CSS，行为标准主要包括对象模型（如W3C DOM）、ECMAScript等。这些标准大部分由W3C起草和发布，也有一些是其他标准组织制订的标准，比如ECMA（European Computer Manufacturers Association）的ECMAScript标准。

**<pre>** 定义预格式文本（通常会**保留空格及换行符**，并使用等宽字体显示，很适合用来表示计算机代码）

for(var i=0; i<9; i++){

    i++;

};

**<address>**定义文档作者或拥有者的联系信息（通常使用斜体显示）

[联系小叉](https://links.jianshu.com/go?to=mailto%3Aciaoca%40gmail.com)

前端开发工程师

**<a>** 定义[链接、锚点](https://links.jianshu.com/go?to=http%3A%2F%2Fciaoca.com%2F)

**<em>** 定义*强调文本*（通常使用斜体显示）

**<strong>** 定义**更为强调的文本**（通常使用粗体显示）

**<mark>** 定义带有标记的文本（通常会高亮显示）

**<time>** 定义时间（通常不会有任何视觉效果）

**<del>** 定义~~被删除文本~~（通常带有删除线）

**<ins>** 定义新插入文本（通常带有下划线）

2015年1月1日是~~星期五~~星期四

**<i>** 定义*斜体文本*

**<b>** 定义**粗体文本**

**<big>** 定义大号文本（通常使用比正文更大的字号显示）

**<small>** 定义小号文本（通常使用比正文更小的字号显示）

**<sup>** 定义上标文本、X2

**<sub>** 定义下标文本、H2O

**<code>** 定义计算机代码文本。This HTML Code.（通常使用等宽字体显示，但**不会保留空格及换行符**，需要保留空格及换行符，请使用

<pre style="box-sizing: border-box; font-size: 12px; font-family: Consolas, Monaco, &quot;Andale Mono&quot;, &quot;Ubuntu Mono&quot;, monospace; margin-top: 0px; margin-bottom: 20px; overflow: scroll auto; overflow-wrap: normal; word-break: break-all; white-space: pre; overscroll-behavior-x: contain; border-radius: 4px; z-index: 0; padding: 1em; line-height: 1.5; color: rgb(204, 204, 204); background: rgb(45, 45, 45); font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;">）[详细描述](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.w3school.com.cn%2Ftags%2Ftag_code.asp)</pre>

**<cite>** 定义引用。可使用该标签对参考文献的引用进行定义，比如书籍或杂志的标题。（通常使用斜体显示）[详细描述](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.w3school.com.cn%2Ftags%2Ftag_cite.asp)

**<q>** 定义短的引用（通常会在两边加双引号）

**<bdo>** 定义文本的方向

后面的文字会从右到左来显示：你已经具备了倒读的能力

**<abbr>** 定义缩写

微软推出的浏览器是 IE 浏览器。（鼠标移到“IE”上看效果）

**<progress>** 定义进度条 

**<meter>** 定义度计量  

【图像】

**<img>** 定义图像

**<map>** 定义可点击区域

**<area>** 定义可点击区域的内部区域

图片左右两边的链接不一样：

![image](https://upload-images.jianshu.io/upload_images/10024246-26b5e0fdbfe7dbe7.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/88/format/webp)

**<figure>** 定义文档中的媒体内容（图片、图表、照片、代码等）

**<figcaption>** 定义 figure 元素的标题

小叉试验场

![image](https://upload-images.jianshu.io/upload_images/10024246-d9651e550e8f1d7a.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/88/format/webp)

**<canvas>** 定义画布（此处不做演示）

【音频/视频】

**<audio>** 定义声音

**<source>** 定义媒体源

**<audio>** 定义视频

**<video>** 定义字幕

【其他】

**<noscript>** 定义针对不支持客户端脚本的用户的替代内容

当前您的浏览器支持JavaScript脚本

**<noframes>** 定义针对不支持框架的用户的替代内容

**<ruby>** 定义 ruby 注释

**<rt>** 定义 ruby 注释的解释

**<rp>** 定义若浏览器不支持 ruby 元素显示的内容
