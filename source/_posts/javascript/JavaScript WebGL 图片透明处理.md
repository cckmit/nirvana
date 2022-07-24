---
title: JavaScript WebGL 图片透明处理
date: 2022-02-24 16:28:03
categories: 前端
---
## 引子

[JavaScript WebGL 使用图片疑惑点](https://github.com/XXHolic/segment/issues/114)中提到两张图片叠加，默认情况下，即使有透明的区域也不会透过看到。现在就来看这个透明的处理。

*   [Origin](https://github.com/XXHolic/segment/issues/115)
*   [My GitHub](https://github.com/XXHolic)

## 关于透明

说到透明，在颜色编码中由 Alpha 通道负责，透明度存储方式有：

*   Premultiplied Alpha ：表示颜色信息在存储的时候会将透明信息与 RGB 相乘，比如 RGB 是 (1, 1, 1)，透明度为 0.5 ，那么存储时为：(0.5, 0.5, 0.5, 0.5) 。
*   Non-premultiplied Alpha ： 表示 RGB 与透明信息分别单独存储，不会相乘，比如 RGB 是 (1, 1, 1)，透明度为 0.5 ，那么存储时为：(1, 1, 1, 0.5)

WebGL 纹理预处理使用 [pixelStorei](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/pixelStorei) 方法指定颜色透明处理方式，如果想要使用 Premultiplied Alpha 方式：

```gl.pixelStorei(gl.UNPACK_PREMULTIPLY_ALPHA_WEBGL, true);```

WebGL 透明处理方式之一是使用 α 混合。

## α 混合

α 混合是使用 α 值(RGBA 中的 A)混合两个以上物体颜色的过程。在这个场景下，绘制了多个纹理且有重叠的地方，假设先绘制纹理 C ，然后再绘制纹理 D ，那么纹理 D 就是源颜色（source color）， 纹理 C 就是目标颜色（destination color）。

想要使用该功能，首先要开启：

```gl.enable(gl.BLEND);```

然后指定混合的方式有：

*   [blendEquation](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/blendEquation)
*   [blendEquationSeparate](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/blendEquationSeparate)
*   [blendFunc](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/blendFunc)
*   [blendFuncSeparate](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/blendFuncSeparate)

### blendEquation

```void blendEquation(enum mode)```

`mode` 取值有：

*   FUNC_ADD : 源颜色分量与目标颜色分量相加。
*   FUNC_SUBTRACT : 源颜色分量减去目标颜色分量。
*   FUNC_REVERSE_SUBTRACT : 目标颜色分量减去源颜色分量。

这个方法只指定了混合的方式，如果要看到最终的效果，需要跟 `blendFunc` 或 `blendFuncSeparate` 方法配合一起使用。看看这篇[文章](http://learnwebgl.brown37.net/11_advanced_rendering/alpha_blending.html?highlight=alpha)最下面的伪代码逻辑会加深理解。

这是[示例](https://xxholic.github.io/lab/segment/98/blend-equation.html)。

### blendEquationSeparate

`void blendEquationSeparate(enum modeRGB, enum modeAlpha)`

这个方法的两个参数取值与 `blendEquation` 方法参数取值一样，区别是把颜色分成了 RGB 和 A 单独的两部分。

### blendFunc

`void blendFunc(enum sfactor, enum dfactor);`

*   sfactor : 常量，源颜色在混合颜色中的权重因子，比 dfactor 多一个值 `SRC_ALPHA_SATURATE` 。
*   dfactor : 常量，目标颜色在混合颜色中的权重因子。

混合颜色的计算方法是：

`混合后颜色 = 源颜色 * sfactor + 目标颜色 * dfactor`

这里计算针对的是每个颜色分量，下面设定 S 代码源颜色，D 代表目标颜色，各个分量用小写 rgba 表示，下面看看权重因子部分常量取值：

| 常量 | R 分量 | G 分量 | B 分量 | A 分量 |
| :-- | :-- | :-- | :-- | :-- |
| ZERO | 0 | 0 | 0 | 0 |
| ONE | 1 | 1 | 1 | 1 |
| SRC_COLOR | S.r | S.g | S.b | S.a |
| ONE_MINUS_SRC_COLOR | (1-S.r) | (1-S.g) | (1-S.b) | (1-S.a) |
| DST_COLOR | D.r | D.g | D.b | D.a |
| ONE_MINUS_DST_COLOR | (1-D.r) | (1-D.g) | (1-D.b) | (1-D.a) |
| SRC_ALPHA | S.a | S.a | S.a | S.a |
| ONE_MINUS_SRC_ALPHA | (1-S.a) | (1-S.a) | (1-S.a) | (1-S.a) |
| DST_ALPHA | D.a | D.a | D.a | D.a |
| ONE_MINUS_DST_ALPHA | (1-D.a) | (1-D.a) | (1-D.a) | (1-D.a) |

还有可以配合方法 [blendColor(red, green, blue, alpha)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/blendColor) 的常量取值：

| 常量 | R 分量 | G 分量 | B 分量 | A 分量 |
| :-- | :-- | :-- | :-- | :-- |
| CONSTANT_COLOR | red | green | blue | alpha |
| ONE_MINUS_CONSTANT_COLOR | (1-red) | (1-green) | (1-blue) | (1-alpha) |
| CONSTANT_ALPHA | alpha | alpha | alpha | alpha |
| ONE_MINUS_CONSTANT_ALPHA | (1-alpha) | (1-alpha) | (1-alpha) | (1-alpha) |

如果不使用 `blendColor` 指定分量，也是可以使用这些常量，因为有默认值：

`gl.getParameter(gl.BLEND_COLOR) // 默认对应分量都是 0`

关于第一个参数多的取值 `SRC_ALPHA_SATURATE` ：

| 常量 | R 分量 | G 分量 | B 分量 | A 分量 |
| :-- | :-- | :-- | :-- | :-- |
| SRC_ALPHA_SATURATE | min(S.a, 1-D.a) | min(S.a, 1-D.a) | min(S.a, 1-D.a) | 1 |

下面这些是示例：

*   没有使用 `blendColor` 方法的[示例](https://xxholic.github.io/lab/segment/98/blend.html)。
*   配合使用 `blendColor` 方法的[示例](https://xxholic.github.io/lab/segment/98/blend-color.html)。

### blendFuncSeparate

`void blendFuncSeparate(enum srcRGB, enum dstRGB, enum srcAlpha, enum dstAlpha)`

这个方法参数取值与 `blendFunc` 方法参数取值一样，区别是把颜色分成了 RGB 和 A 单独的两部分。
>转载：https://segmentfault.com/a/1190000041293517