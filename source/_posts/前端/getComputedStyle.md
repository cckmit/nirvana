---
title: getComputedStyle
date: 2021-11-30 16:21:13
category: javascript
---
DOM2 Style 在 `document.defaultView` 上增加了 `getComputedStyle()`方法，该方法返回一个 `CSSStyleDeclaration`对象（与 style 属性的类型一样），包含元素的计算样式。
```
//API
document.defaultView.getComputedStyle(element[,pseudo-element])
// or
window.getComputedStyle(element[,pseudo-element])
```
这个方法接收两个参数：要取得计算样式的元素和伪元素字符串（如":after"）。如果不需要查询伪元素，则第二个参数可以传 null。
```
<!DOCTYPE html>
<html>
  <head>
    <style type="text/css">
      #myDiv {
        background-color: blue;
        width: 100px;
        height: 200px;
      }
    </style>
  </head>
  <body>
    <div id="myDiv" style="background-color: red; border: 1px solid black"></div>
  </body>
  <script>
    function getStyleByAttr(obj, name) {
      return window.getComputedStyle ? window.getComputedStyle(obj, null)[name] : obj.currentStyle[name]
    }
    let node = document.getElementById('myDiv')
    console.log(getStyleByAttr(node, 'backgroundColor'))
    console.log(getStyleByAttr(node, 'width'))
    console.log(getStyleByAttr(node, 'height'))
    console.log(getStyleByAttr(node, 'border'))

  </script>
</html>
```
和 style 的异同
`getComputedStyle` 和 `element.style` 的相同点就是二者返回的都是 `CSSStyleDeclaration` 对象。而不同点就是：

- `element.style` 读取的只是元素的内联样式，即写在元素的 style 属性上的样式；而 `getComputedStyle` 读取的样式是最终样式，包括了内联样式、嵌入样式和外部样式。
- `element.style` 既支持读也支持写，我们通过 element.style 即可改写元素的样式。而 `getComputedStyle` 仅支持读并不支持写入。我们可以通过使用 `getComputedStyle` 读取样式，通过 `element.style` 修改样式
