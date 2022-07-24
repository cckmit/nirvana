---
title: input 如何监听输入中文
date: 2021-09-16 16:28:03
categories: 前端
---

## input 组合事件

`compositionEvent` 组合事件是拆分了不同步骤的事件的组合，是由 `compositionStart`，`compositionUpdate` 和 `compositionEnd`三个事件组合而成。

Start 和 End 事件只执行一次，Update 会执行多次。
输入前，会触发 Start 和 Update 事件；没有选中中文之前，会一直触发 Update 事件；选中文字后，会触发 End 事件；一个组合事件完成，以此循环。

### compositionstart

输入法编辑器开始新的输入合成时会触发 `compositionstart`事件。
例如：当用户使用拼音输入法开始输入汉字时，这个事件就会被触发。

### compositionupdate

字符被输入到一段文字的时候（这些可见字符的输入可能需要一连串的键盘操作、语音识别或者点击输入法的备选词）会触发 `compositionupdate` 事件。

### compositionend

当文本段落的组成完成或取消时, `compositionend` 事件将被触发。

> **注意：清除键、粘贴、英文字母和数字是不会触发这几个事件的。Start 和 Update 事件会在 onChange 之前触发，End 事件在onChange之后触发。**

## 例子

### 简单使用

```
<input 
  placeholder="Basic usage" 
  onChange={e => console.log('onchange', e.target.value)}
  onCompositionStart={e => console.log('onCompositionStart', e.target.value)}
  onCompositionUpdate={e => console.log('onCompositionUpdate', e.target.value)}
  onCompositionEnd={e => console.log('onCompositionEnd', e.target.value)}
/>
```

输入英文'suzhou', 只会触发onChange事件：

![](https://upload-images.jianshu.io/upload_images/10024246-f0f894eca2168e63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


输入中文‘苏州’，触发事件如下：

![](https://upload-images.jianshu.io/upload_images/10024246-f318123f13b265e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


输入中文‘苏州’后， 删除‘州’，只会触发onChange事件：

![](https://upload-images.jianshu.io/upload_images/10024246-4f57079a3e6b5cf5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


> 直接使用 `onCompositionEnd`事件，只能监听到输入改变，不能监听到删除。

### 监听输入的中文字符变化，如何实现？

```
let isOnComposition = false

function handleComposition(e) {
  console.log(e.type, e.target.value)
  if (e.type === 'compositionend') {
    isOnComposition = false
    if (!isOnComposition) {
      onChange(e);
    }
  } else {
    isOnComposition = true
  }
}

function onChange(e) {
  if (!isOnComposition) {
    console.log('onChange', e.target.value)
  }
}

ReactDOM.render(
 <input
  placeholder="Basic usage" 
  onChange={onChange}
  onCompositionStart={handleComposition} 
  onCompositionUpdate={handleComposition} 
  onCompositionEnd={handleComposition} 
/>, mountNode);
```

输入英文‘suzhou’，只会触发 onchange 事件：

![](https://upload-images.jianshu.io/upload_images/10024246-edf3cbb645a21750.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


输入中文‘苏州’，然后删除‘州’，触发事件如下：

![](https://upload-images.jianshu.io/upload_images/10024246-c651efc5dad5981b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


> 组合事件与 'onchange' 结合使用，就实现了监听中文字符输入变化。

### input 在手机端触发软键盘的表现

输入框聚焦（`触发 focus 事件`）后，会弹出软键盘， 表现如下：

**IOS 软键盘弹起表现**

输入框获取焦点后，键盘弹起，页面（`webview`）没有被压缩，页面可使区域高度（`height`）还是原高度，只是页面（`webview`）整体往上滚了，且最大滚动高度（`scrollTop`）为软键盘高度。

**Android 软键盘弹起表现**

输入框获取焦点后，键盘弹起，页面（`webview`）高度会发生改变，高度为可视区高度（原高度减去软键盘高度），除了因为页面内容被撑开可以产生滚动，`webview` 本身不能滚动。

**IOS 软键盘收起表现**

触发软键盘上的“收起”按钮键盘或者输入框以外的页面区域时，输入框失去焦点，软键盘收起，会触发 `blur`事件。

**Android 软键盘收起表现**

触发输入框以外的区域时，输入框失去焦点，软键盘收起。但是，触发键盘上的收起按钮键盘时，软键盘收起，输入框不会失去焦点，不会触发 `blur`事件。

综合上面键盘弹起和收起在 `IOS` 和 `Android` 上的不同表现，需要分开处理来监听软键盘的弹起和收起：

*   在 `IOS` 上，监听输入框的 `focus` 事件来获知软键盘弹起，监听输入框的 `blur` 事件获知软键盘收起。
*   在 `Android` 上，监听 `webview` 高度会变化，高度变小获知软键盘弹起，否则软键盘收起。
