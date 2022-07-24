---
title: 网页性能衡量之网页fps 检测
date: 2021-12-12 16:28:03
categories: 前端
---
浏览器对于网页的渲染方式和游戏中的渲染方式其实并不相同，在游戏中由于画面是不断变化的，需要持续渲染才能形成流畅的画面。但是浏览器中的网页并不一样如果你不进行一些操作网页显示的内容可能并不会发生变化，所以并不需要实时渲染。

fps主要用来衡量动画的性能，具体到页面那就是页面动画的性能，而不是整个页面的性能。图片滑动是动画，页面滚动本质上也是动画。当页面静止并无其他动画用fps去衡量是不恰当的。

但是，fps 帧率作为网页性能衡量标准的其中之一，在某些业务场景下也不能忽视，我们可以通过以下几种方式对web页面进行监控：

- 1、通过chrome开发者工具进行监控。
- 2、通过对页面添加 Javascript 代码实现。

方法一、通过chrome开发者工具进行监控

这种方式一般在开发阶段使用：

1、首先，在chrome浏览器下 按下 F12 打开chrome开发者工具-performance，点击reload 按钮。

![](https://upload-images.jianshu.io/upload_images/10024246-cf288a76125f028e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2、等待加载完毕，你就可以看到下图的各维度的分析结果，但这不是今天我们要讨论的重点。

3、点击左上角工具栏，找到More tools中 的rendering，勾选上FPS meter

![](https://upload-images.jianshu.io/upload_images/10024246-3e4a95b0949e03d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-77f111a030e30a75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

方法二、通过给网页添加一段 Javascript 代码，在页面左上角就会显示当前网页的 fps 值。

demo：

```
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>FPS Test Page</title>
  </head>
  <body>
    <script> // 创建 fps 面板展示元素
      var fpsPanel = document.createElement("div");
      fpsPanel.setAttribute("id", "fps");
      fpsPanel.style.position = "fixed";
      fpsPanel.style.left = "3px";
      fpsPanel.style.top = "3px";
      fpsPanel.style.color = "red";
      fpsPanel.style.zIndex = 10000;
      // 将面板插入到 body
      document.body.append(fpsPanel);
      // fps 监测逻辑实现
      var showFPS = (function () {
        var requestAnimationFrame =
          window.requestAnimationFrame ||
          window.webkitRequestAnimationFrame ||
          window.mozRequestAnimationFrame ||
          window.oRequestAnimationFrame ||
          window.msRequestAnimationFrame ||
          function (callback) {
            window.setTimeout(callback, 1000 / 60);
          };
        var e, pe, pid, fps, last, offset, step, appendFps;

        fps = 0;
        last = Date.now();
        step = function () {
          offset = Date.now() - last;
          fps += 1;
          if (offset >= 1000) {
            last += offset;
            appendFps(fps);
            fps = 0;
          }
          requestAnimationFrame(step);
        };
        appendFps = function (fps) {
          // 打印 fps
          console.log(fps + "FPS");
          // 修改面板显示的值
          fpsPanel.innerHTML = fps + "FPS";
        };
        step();
      })(); </script>
  </body>
</html>
```
