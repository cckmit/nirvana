---
title: Element Plus 正式版发布啦！🎉🎉
date: 2022-02-07 16:28:03
categories: 资讯
---
![](https://upload-images.jianshu.io/upload_images/10024246-45ff428d61d2aef9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

今天，我们非常高兴地宣布 Element Plus 稳定版正式发布。自第一个 commit 起，经过 1 年零 7 个月的持续迭代开发，总计 2635 commits，经过 256 位贡献者所提交的 2494 个 PR，137 个 Alpha 与 Beta 版本，在社区每一位同学的参与帮助下，Element Plus 的第一个正式版终于和大家见面。

## 重大更新

### TypeScript 与 Vue 3

Element Plus 使用 TypeScript 与 Vue 3.2 开发，提供完整的类型定义文件。并使用 Composition API 降低耦合，简化逻辑。

### 兼容性

由于 Vue 3 不再兼容 IE，所以 Element Plus 也提高了最低兼容的版本。

|![](https://upload-images.jianshu.io/upload_images/10024246-3ff019acf7f344df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)| ![](https://upload-images.jianshu.io/upload_images/10024246-6b41466006b5a047.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|![](https://upload-images.jianshu.io/upload_images/10024246-3822e170339157a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|![](https://upload-images.jianshu.io/upload_images/10024246-fc97d8de05d854ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) |
| --- | --- | --- | --- |
| Edge ≥ 79 | Firefox ≥ 78 | Chrome ≥ 64 | Safari ≥ 12 |

如果想在低版本浏览器上正常使用 Element Plus，请自行使用 Babel、ESBuild 或其他转换工具，并引入相应的 polyfill。
值得注意的是，Element Plus 使用到了 ResizeObserver，如有兼容性需求需要您自行引入 [resize-observer-polyfill](https://www.npmjs.com/package/resize-observer-polyfill)。详情请参阅 [ResizeObserver 的兼容性](https://caniuse.com/resizeobserver)。

### ESM 与 CJS

Element Plus 同时支持 ESM、CJS 与 UMD 格式。一般情况下无需留意导入的格式，构建工具会自动匹配并转换成目标格式，同时无需额外配置，自身支持按需加载能力。

### 设计

组件大小体系由 `default/medium/small/mini` 切换为更自然的 `large/default/small`，以 `default` 为基础，需要加大则选择 `large`，需要缩小则选择 `small`。

padding 方面则优化为更通用的 4px 体系，采用 4/8 px 作为原子单位控制整个系统的 padding 一致性。即大组件 padding 也大，小组件 padding 也小。具体细节请参阅 [size 修改说明](https://link.segmentfault.com/?enc=DJe7FfciNk%2FD9HDZJt2BQw%3D%3D.EBHb3CwoW%2B53Y9PhPdu7lop4QYehNzUELGc%2FiXgfoRn6vN4lWox5EiIN7oJv7hQSJCkAiVdMEepv%2FEL0SoLKLxkAlg0JZt6IuogNpgfvemw%3D)。

### 图标

为了使用 Element Plus 内置的图标，用户往往需要引用一个 `~75KB` 的字体文件，以及 1 个额外的网络请求，这个在大多数情况下属于完全不需要的开销，对体积以及首页加载速度很在意的用户来说，这属于是一个长久的痛点。

因此我们把所有的 Font Icon 都改为了 Inline Vue [SVG 组件](https://www.npmjs.com/package/@element-plus/icons-vue)，也就是说所有的组件都是跟随你的打包代码一起放在同一个请求内，这样就不会产生额外的网络请求去请求字体文件，也不会带来网络请求失败导致字体渲染不出来的小方块，并且图标的清晰度也会更好。

您可以下载旧版本的字体文件来让老项目保持兼容。

*   [unpkg.com/element-plus@1.1.0-beta.24/theme-chalk/base.css](https://unpkg.com/element-plus@1.1.0-beta.24/theme-chalk/base.css)
*   [unpkg.com/browse/element-plus@1.1.0-beta.24/theme-chalk/fonts](https://unpkg.com/browse/element-plus@1.1.0-beta.24/theme-chalk/fonts/)

### 配置

Element Plus 新增了一个全局配置管理的组件 `config-provider`，以替代 Element UI 的全局配置 `Vue.prototype.$ELEMENT`。
您可以通过以下两种方式来进行全局配置。

```
// 方式一 main.ts
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import App from './App.vue'

createApp(App).use(ElementPlus, { size: 'small' }).mount('#app')

// 方式二 APP.vue
<template>
  <el-config-provider :size="small">
    <el-button>Button</el-button>
  </el-config-provider>
</template>
```

如需了解更多 API 变动细节，请参阅 [Element Plus 不兼容变化和升级指南](https://github.com/element-plus/element-plus/discussions/5657)。

### 新组件和设计资源

在迁移完现有组件的基础上，正式版本中增加了 `Space`, `Skeleton`, `Empty` 和 `Affix` 四个全新组件丰富开发者的选择。以及增加了使用虚拟滚动的 `Select-V2` 组件来优化长列表的展示性能问题。

同时我们也制作上传了包含所有组件信息的最新 Figma [设计资源](https://element-plus.org/zh-CN/resource/index.html) 文件，方便产品经理和设计师的使用。

正式版的发布意味着整体迁移适配工作的结束，API 设计基本稳定，但这只是一个开始。在后续的迭代工作中，我们将集中精力在优化交互细节，新增额外功能上，包括但不仅限以下内容：

### 暗色主题

正式版中，我们集成了 `CSS Variables` 的全新能力，这将比之前的 `SASS` 变量配置模式更方便且性能更好。在这套能力的基础上，我们正在紧张开发暗色主题，很快会在后续版本中与大家见面。

### 高性能表格组件

在 Beta 发布的时候，我们提到过提供使用虚拟化能力的表格，来优化大数据量情况下的 Table 组件性能。但本次的正式版发布暂未包含这部分功能，而会在后续的迭代中加入。一直没有把这个功能落地下来有很大一部分原因是，我们一直在探索到底哪一种方式是更加适合用户的。是我们直接加入虚拟化的表格渲染引擎，亦或是我们提供渲染接口让用户自己添加虚拟化的组件（类似 vue-virtual-scroller 这样的组件）来自行渲染。后续我们会参考结合现在市面上相关组件的实现，提供一套解决方案，让 Element Plus 的 Table 组件更加好用。

## 相关生态

*   [Element Plus Icons](https://github.com/element-plus/element-plus-icons) - Element Plus 图标集合。
*   [Element Plus Playground](https://github.com/element-plus/element-plus-playground) - 您可以点击[此处](https://element-plus.run)尝试！ 😆
*   [Element Plus Vite Starter](https://github.com/element-plus/element-plus-vite-starter) - Vite 快速上手模板。
*   [unplugin-element-plus](https://github.com/element-plus/unplugin-element-plus) - Element Plus 按需加载样式插件。
*   [Design Materials](https://github.com/element-plus/design-materials) - Element Plus 社区的 Logo、表情包与其他资源。
*   [awesome-element-plus](https://github.com/sxzz/awesome-element-plus) - Element Plus 相关库、模板与资源的列表。
