---
title: HTML 5 深色模式适配 (prefers-color-scheme)
date: 2022-04-18 16:28:03
categories: 前端
---
伴随 mac Mojave 发布的系统级别的黑色模式，已经存在很久了。CSS 随之推出了 `[prefers-color-scheme](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme)` 的 media 选择器，使得网页能够适配黑色主题。

## 语法

```
@media (prefers-color-scheme: light) {
  /** ... */
}
@media (prefers-color-scheme: dark) {
  /** ... */
}
@media (prefers-color-scheme: no-preference) {
  /** ... */
}
```

其中 mode 有如下可能的取值：

*   `no-preference`：类似于缺省
*   `light`：亮色模式下命中
*   `dark`：黑色模式下命中
## 示例

以下是来自 [MDN 的示例](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme)：

```
<style>
  .day {
    background: #eee;
    color: black;
  }
  .night {
    background: #333;
    color: white;
  }

  @media (prefers-color-scheme: dark) {
    .day.dark-scheme {
      background: #333;
      color: white;
    }
    .night.dark-scheme {
      background: black;
      color: #ddd;
    }
  }

  @media (prefers-color-scheme: light) {
    .day.light-scheme {
      background: white;
      color: #555;
    }
    .night.light-scheme {
      background: #eee;
      color: black;
    }
  }

  .day,
  .night {
    display: inline-block;
    padding: 1em;
    width: 7em;
    height: 2em;
    vertical-align: middle;
  }
</style>
<div class="day">Day (initial)</div>
<div class="day light-scheme">Day (changes in light scheme)</div>
<div class="day dark-scheme">Day (changes in dark scheme)</div>
<br />

<div class="night">Night (initial)</div>
<div class="night light-scheme">Night (changes in light scheme)</div>
<div class="night dark-scheme">Night (changes in dark scheme)</div>
```

实际运行效果：

  <video src="https://vdn3.vzuu.com/SD/6ee4a5a0-bb5e-11eb-9421-82ae8ff31740.mp4?disable_local_cache=1&amp;c=avc.0.0&amp;auth_key=1650294580-0-0-3f8f0304d5c27a71139a83aa6368f76d&amp;f=mp4&amp;bu=pico&amp;expiration=1650294580&amp;v=tx" data-thumbnail="https://pic1.zhimg.com/v2-fc0abdd9ba0ab815509511c99eaa1ed4_b.jpg" poster="https://pic1.zhimg.com/v2-fc0abdd9ba0ab815509511c99eaa1ed4_b.jpg" data-size="normal" preload="metadata" loop="" playsinline="" controls=""></video>

利用 prefers-color-scheme 适配黑色模式的运行效果
