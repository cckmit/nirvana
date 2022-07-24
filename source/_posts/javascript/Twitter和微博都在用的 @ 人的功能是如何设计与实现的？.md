---
title: Twitter和微博都在用的 @ 人的功能是如何设计与实现的？
date: 2022-04-18 16:28:03
categories: 前端
---
## 背景

第一次使用@人功能到现在已经有差不多10年了，初次使用是通过微博体验的。@人的功能现在遍布各种应用，只要是涉及社交、办公等场景，就是一个必不可少的功能。最近也在调研 IM 的各种功能的实现方案，所以也稍微地了解了下@人功能的前端实现。
![](https://upload-images.jianshu.io/upload_images/10024246-a122d3c038b07c0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 业内实现

### 微博

![](https://upload-images.jianshu.io/upload_images/10024246-a8f01dfdf23ed09e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-c480a01a244e88ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-101e22f53078c4c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

微博的实现比较简单，就是通过正则匹配，最后用空格表示匹配结束，所以实现上是直接使用了textarea标签。但是这个实现必须依赖的一个事情是：用户名必须唯一。微博的用户名就是唯一的，所以正则所匹配到的ID，一般的可以映射到唯一的一个用户上（除非ID不存在）。整体的输出比较宽松，你可以构造任何不存在的ID进行@操作。

### Twitter

![](https://upload-images.jianshu.io/upload_images/10024246-d8aaa34023a6439c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-ee9e81ab71e5b2e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Twitter 的实现跟微博类似，也是以@开始，空格结尾做匹配。但是使用的是 contenteditable 这个属性进行富文本操作，相似之处在于 Twitter 的 ID 也是唯一，但是可以通过昵称进行搜索，然后转化成 ID，这一点在体验上好了不少。

## 基本思路

1.  监听用户输入，匹配用户以@开头的文字。
2.  调用搜索弹窗，展示搜索出来的用户列表。
3.  监听上、下、回车键控制列表选择，监听ESC键关闭搜索弹窗。
4.  选择需要@的用户，把对应的HTML文本替换到原文本上。在HTML文本上添加用户的元数据。

一般来说，如果像平常用的 Lark 搜索，我们是不会通过唯一的『工号』去进行搜索，而是通过名字，但是名字会出现重复，所以就不太适合用textarea的方式，而是用contenteditable，把@文本替换成HTML标签特殊化标记。

## 关键步骤

1.  ### 获得用户的光标位置

想要获得用户输入的字符串，然后替换进去，第一步就是需要获得用户所在的光标。要获取光标信息，那就要先了解什么是『**选择（Selection）** 』和『**范围（Range）** 』。

#### 范围（Range）

`Range`本质上是一对“边界点”：范围起点和范围终点。

每个点都被表示为一个带有相对于起点的相对偏移（offset）的父 DOM 节点。如果父节点是元素节点，则偏移量是子节点的编号，对于文本节点，则是文本中的位置。

例如：

```let range = new Range();```
然后使用 `range.setStart(node, offset)` 和 `range.setEnd(node, offset)` 来设置选择边界。

假设 HTML 片段是这样的：

```<p id="p">Example: <i>italic</i> and <b>bold</b></p>```

选择 `"Example: <i>italic</i>"`，它是 `<p>` 的前两个子节点（文本节点也算在内）：

![](https://upload-images.jianshu.io/upload_images/10024246-ae53352e654f9a28.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<p id="p">Example: <i>italic</i> and <b>bold</b></p>

<script> let range = new Range();

  range.setStart(p, 0);

  range.setEnd(p, 2);

  // 范围的 toString 以文本形式返回其内容（不带标签）

  alert(range); // Example: italic

  document.getSelection().addRange(range); </script>
```

*   `range.setStart(p, 0)` —— 将起点设置为 `<p>` 的第 0 个子节点（即文本节点 `"Example: "`）。

*   `range.setEnd(p, 2)` —— 覆盖范围至（但不包括）`<p>` 的第 2 个子节点（即文本节点 `" and "`，但由于不包括末节点，所以最后选择的节点是 `<i>`）。

如果像这样操作：

![](https://upload-images.jianshu.io/upload_images/10024246-b93c9efdb5cc74e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这也是可以做到的，只需要将起点和终点设置为文本节点中的相对偏移量即可。

我们需要创建一个范围：

*   从 `<p>` 的第一个子节点的位置 2 开始（选择 "Ex**ample:** " 中除前两个字母外的所有字母）

*   到 `<b>` 的第一个子节点的位置 3 结束（选择 “**bol**d” 的前三个字母，就这些）：

```
<p id="p">Example: <i>italic</i>  and <b>bold</b></p>

<script> let range = new Range();

  range.setStart(p.firstChild, 2);

  range.setEnd(p.querySelector('b').firstChild, 3);

  alert(range); // ample: italic and bol

  window.getSelection().addRange(range); </script>
```

`range` 对象具有以下属性：

![](https://upload-images.jianshu.io/upload_images/10024246-5b7d945cdac4e11e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   `startContainer`，`startOffset` —— 起始节点和偏移量，

*   在上例中：分别是 `<p>` 中的第一个文本节点和 `2`。

*   `endContainer`，`endOffset` —— 结束节点和偏移量，

*   在上例中：分别是 `<b>` 中的第一个文本节点和 `3`。

*   `collapsed` —— 布尔值，如果范围在同一点上开始和结束（所以范围内没有内容）则为 `true`，

*   在上例中：`false`

*   `commonAncestorContainer` —— 在范围内的所有节点中最近的共同祖先节点，

*   在上例中：`<p>`

#### 选择（Selection）

`Range` 是用于管理选择范围的通用对象。

文档选择是由 `Selection` 对象表示的，可通过 `window.getSelection()` 或 `document.getSelection()` 来获取。

根据 Selection API 规范<sup>[1]</sup> 一个选择可以包括零个或多个范围。不过实际上，只有 Firefox 允许使用 Ctrl+click (Mac 上用 Cmd+click) 在文档中选择多个范围。

这是在 Firefox 中做的一个具有 3 个范围的选择的截图：

![](https://upload-images.jianshu.io/upload_images/10024246-ee655aaa28d7334b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其他浏览器最多支持 1 个范围。正如我们将看到的，某些 `Selection` 方法暗示可能有多个范围，但同样，在除 Firefox 之外的所有浏览器中，范围最多是 1。

与范围相似，选择的起点称为“锚点（anchor）”，终点称为“焦点（focus）”。

主要的选择属性有：

*   `anchorNode` —— 选择的起始节点，

*   `anchorOffset` —— 选择开始的 `anchorNode` 中的偏移量，

*   `focusNode` —— 选择的结束节点，

*   `focusOffset` —— 选择开始处 `focusNode` 的偏移量，

*   `isCollapsed` —— 如果未选择任何内容（空范围）或不存在，则为 `true` 。

*   `rangeCount` —— 选择中的范围数，除 Firefox 外，其他浏览器最多为 `1`。

看完上面，不知道了解了没？没关系，我们继续往下。综上所述，一般我们只有一个 Range，当我们的光标在 contenteditable 的 div 上闪动的时候，其实就有了一个 Range，这个 Range 的开始和结束位置都是一样的。另外，我们还可以直接通过 `Selection.focusNode`获取到对应的节点，通过 `Selection.focusOffset` 获取到对应的偏移量。就像下图：

![](https://upload-images.jianshu.io/upload_images/10024246-98b74575c610a1e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样，我们就获取到了光标的位置以及对应的TextNode对象。

2.  ### 获取需要@的用户

从步骤一我们获得了光标在对应Node节点的偏移量，以及对应的Node节点。那么就可以通过`textContent`方法获取整个文本。

一般来说，通过一个简单的正则就可以获取@的内容了：

```
// 获取光标位置

const getCursorIndex = () => {

  const selection = window.getSelection();

  return selection?.focusOffset;

};

 // 获取节点

const getRangeNode = () => {

  const selection = window.getSelection();

  return selection?.focusNode;

};

 // 获取 @ 用户

const getAtUser = () => {

  const content = getRangeNode()?.textContent || "";

  const regx = /@([^@\s]*)$/;

  const match = regx.exec(content.slice(0, getCursorIndex()));

  if (match && match.length === 2) {

    return match[1];

  }

  return undefined;

};
```
因为@的插入可能是末尾，可能是中间，所以我们在判断前，还需要截取光标前的文本。

![](https://upload-images.jianshu.io/upload_images/10024246-0553f66ab081ef13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以简单地slice一下就好了：

```content.slice(0, getCursorIndex())```

3.  ### 弹窗展示以及按键拦截

弹窗是否展示的逻辑，跟判断@用户类似，都是同一个正则。

```
// 是否展示 @

const showAt = () => {

  const node = getRangeNode();

  if (!node || node.nodeType !== Node.TEXT_NODE) return false;

  const content = node.textContent || "";

  const regx = /@([^@\s]*)$/;

  const match = regx.exec(content.slice(0, getCursorIndex()));

  return match && match.length === 2;

};
```

弹窗需要出现在正确的位置，幸好现代浏览器有不少好用的API。

```
const getRangeRect = () => {

  const selection = window.getSelection();

  const range = selection?.getRangeAt(0)!;

  const rect = range.getClientRects()[0];

  const LINE_HEIGHT = 30;

  return {

    x: rect.x,

    y: rect.y + LINE_HEIGHT

  };

};
```

![](https://upload-images.jianshu.io/upload_images/10024246-5f86034c9bf6fa0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当出现弹窗之后，我们还需要拦截掉输入框的『上』、『下』、『回车』的操作，否则在输入框响应这些按键会让光标位置偏移到其他地方。

```
const handleKeyDown = (e: any) => {

    if (showDialog) {

      if (

        e.code === "ArrowUp" ||

        e.code === "ArrowDown" ||

        e.code === "Enter"

      ) {

        e.preventDefault();

      }

    }

  };
```
然后在弹窗里面监听这些按键，实现上下选择、回车确定、关闭弹窗的功能。

```
const keyDownHandler = (e: any) => {

      if (visibleRef.current) {

        if (e.code === "Escape") {

          props.onHide();

          return;

        }

        if (e.code === "ArrowDown") {

          setIndex((oldIndex) => {

            return Math.min(oldIndex + 1, (usersRef.current?.length || 0) - 1);

          });

          return;

        }

        if (e.code === "ArrowUp") {

          setIndex((oldIndex) => Math.max(0, oldIndex - 1));

          return;

        }

        if (e.code === "Enter") {

          if (

            indexRef.current !== undefined &&

            usersRef.current?.[indexRef.current]

          ) {

            props.onPickUser(usersRef.current?.[indexRef.current]);

            setIndex(-1);

          }

          return;

        }

      }

    };
```
4.  ### 替换@文本为定制标签
![](https://upload-images.jianshu.io/upload_images/10024246-76ada5fee28fe14f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1.  #### 把原来的 TextNode 进行切块

假如文本是：『请帮我泡一杯咖啡@ABC，这是后面的内容』

那么我们需要根据光标的位置，替换掉@ABC文本，然后分成前后两块：『请帮我泡一杯咖啡』、『这是后面的内容』。

2.  #### 创建 At 标签

为了能实现删除键能把删除全部删除，需要把 at 标签的内容包裹起来。这是第一版写的一个标签，但是如果直接用会有点小问题，留着后续再讨论。

```
const createAtButton = (user: User) => {

  const btn = document.createElement("span");

  btn.style.display = "inline-block";

  btn.dataset.user = JSON.stringify(user);

  btn.className = "at-button";

  btn.contentEditable = "false";

  btn.textContent = `@${user.name}`;

  return btn;

};
```

3.  #### 把标签插进去

首先，我们可以获取 focusNode 节点，然后就可以获取它的父节点以及兄弟节点。现在需要做的是，把旧的文本节点删除，然后在原来的位置上依次插入『请帮我泡一杯咖啡』、【@ABC】、『这是后面的内容』。

```
parentNode.removeChild(oldTextNode);

    // 插在文本框中

    if (nextNode) {

      parentNode.insertBefore(previousTextNode, nextNode);

      parentNode.insertBefore(atButton, nextNode);

      parentNode.insertBefore(nextTextNode, nextNode);

    } else {

      parentNode.appendChild(previousTextNode);

      parentNode.appendChild(atButton);

      parentNode.appendChild(nextTextNode);

    }
```

4.  #### 重置光标的位置

我们这一顿操作之前，因为原来的文本节点丢失，所以我们的光标也失去了。这时候就需要重新把光标定位到 at 标签之后。简单来说就是把光标定位到 nextTextNode 节点之前即可。

```
// 创建一个 Range，并调整光标

    const range = new Range();

    range.setStart(nextTextNode, 0);

    range.setEnd(nextTextNode, 0);

    const selection = window.getSelection();

    selection?.removeAllRanges();

    selection?.addRange(range);
```
5.  #### 优化 at 标签

第2步中，我们创建了 at 标签，但是会有点小问题。

![](https://upload-images.jianshu.io/upload_images/10024246-a51a9db9b155ef68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这时候光标就定位到了『按钮边框内』，但光标的位置实际上是正确的。

为了优化这个问题，首先想到的是在`nextTextNode`中添加一个『0宽字符』：\u200b

```
// 添加 0 宽字符

const nextTextNode = new Text("\u200b" + restSlice);

// 定位光标时，移动一位

const range = new Range();

range.setStart(nextTextNode, 1);

range.setEnd(nextTextNode, 1);
```
![](https://upload-images.jianshu.io/upload_images/10024246-c725d4ac5acce700.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


但是，事情没那么简单。因为我发现如果往前可能也会这样……
![](https://upload-images.jianshu.io/upload_images/10024246-be97d6605c888379.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后一想：把内容区弄宽一点不就行了？比如左右加个空格？然后就把标签包裹了一层……

```
const createAtButton = (user: User) => {

  const btn = document.createElement("span");

  btn.style.display = "inline-block";

  btn.dataset.user = JSON.stringify(user);

  btn.className = "at-button";

  btn.contentEditable = "false";

  btn.textContent = `@${user.name}`;

  const wrapper = document.createElement("span");

  wrapper.style.display = "inline-block";

  wrapper.contentEditable = "false";

  const spaceElem = document.createElement("span");

  spaceElem.style.whiteSpace = "pre";

  spaceElem.textContent = "\u200b";

  spaceElem.contentEditable = "false";

  const clonedSpaceElem = spaceElem.cloneNode(true);

  wrapper.appendChild(spaceElem);

  wrapper.appendChild(btn);

  wrapper.appendChild(clonedSpaceElem);

  return wrapper;

};
```

穷人粗糙版 at 人，最终完结~
![](https://upload-images.jianshu.io/upload_images/10024246-4b5798864cd2a0ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 总结

前端富文本的坑确实比较多，之前没怎么了解过这部分的知识。

虽然整个过程很粗糙，但是道理是这么个道理。

如果有兴趣，也可以到 Playground 玩一玩。

不完善的地方很多，有更好的方式可以共同讨论下。

## Playground

https://codesandbox.io/s/gallant-euclid-4bxsi?file=/src/Editor.tsx:1247-1985

## 站在巨人的肩膀

现代 JavaScript 教程<sup>[2]</sup>

MDN<sup>[3]</sup>

### 参考资料

[1]

Selection API 规范: *https://www.w3.org/TR/selection-api/* [2]

现代 JavaScript 教程: *https://zh.javascript.info/selection-range* [3]

MDN: *https://developer.mozilla.org/en-US/docs/Web/API/Range/getClientRects*
>原文：https://mp.weixin.qq.com/s/YP6H6CHkUd97ThDtEoXzaw