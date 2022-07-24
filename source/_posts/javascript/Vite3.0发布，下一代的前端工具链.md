---
title: Vite3.0发布，下一代的前端工具链
date: 2022-07-17 14:27:21
categories: 前端
---
距离 v2 发布 16 个月后，Vite 3.0 现已正式[发布](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fblog%2Fannouncing-vite3.html)。公告指出，去年 2 月 Vite 2 发布以来，其采用率就在不断增长；每周 npm 下载量超过 100 万次，迅速形成了庞大的生态系统。Vite 正在推动 Web 框架的新一轮创新竞赛。

“我们决定至少每年发布一个新的 Vite 主要版本，以配合 Node.js 的 EOL，并借此机会定期审查 Vite 的 API，为生态系统中的项目提供简短的迁移路径。”

![](https://upload-images.jianshu.io/upload_images/10024246-608874f16d477752.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Vite 3.0 更新内容主要包括：

#### **新文档**

可前往 [vitejs.dev](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2F) 使用新的 v3 文档。Vite 现在使用新的 [VitePress](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitepress.vuejs.org%2F) 默认主题，在其他 features 之间具有一个 Dark 模式。

![](https://upload-images.jianshu.io/upload_images/10024246-57e41779b17a63e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


生态系统中的几个项目已经迁移到这里（参见 [Vitest](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitest.dev%2F)、[vite-plugin-pwa](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvite-plugin-pwa.netlify.app%2F) 和 [VitePress](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitepress.vuejs.org%2F) 本身）。如果你需要访问 Vite 2 文档，它们将保留在 [v2.vitejs.dev](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fv2.vitejs.dev%2F)。还有一个新的 [main.vitejs.dev](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fmain.vitejs.dev%2F) 子域，其中对 Vite 主分支的每个提交都是自动部署的。这在测试 beta 版本或为核心开发做出贡献时很有用。

以及添加了一个正式的西班牙语翻译：

*   [简体中文](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fcn.vitejs.dev%2F)
*   [日本语](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fja.vitejs.dev%2F)
*   [西班牙文](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fes.vitejs.dev%2F)

#### **创建 Vite Starter Templates**

[create-vite](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fguide%2F%23trying-vite-online) 模板是一个很好的工具，可以用你最喜欢的框架快速测试 Vite。在 Vite 3 中，所有的模板都有一个与新文档一致的新主题。在线打开它们并立即开始使用 Vite 3：

[![](https://upload-images.jianshu.io/upload_images/10024246-e1b1fdc17af47b11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvite.new%2F)[![](https://upload-images.jianshu.io/upload_images/10024246-2e4a65ee1fafd37c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvite.new%2Fvue) [![](https://upload-images.jianshu.io/upload_images/10024246-b476ba62e3227f39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvite.new%2Fsvelte) [![](https://upload-images.jianshu.io/upload_images/10024246-60624483e0de6228.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvite.new%2Freact) [![](https://upload-images.jianshu.io/upload_images/10024246-93354ec76b746e70.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvite.new%2Fpreact) [![](https://upload-images.jianshu.io/upload_images/10024246-684cce7001d34ebb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvite.new%2Flit) 

现在所有模板都共享这个主题。对于更完整的解决方案，包括 linting、测试设置和其他功能；有一些框架的官方 Vite-powered 模板，如 [create-vue](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fgithub.com%2Fvuejs%2Fcreate-vue) 和 [create-svelte](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fgithub.com%2Fsveltejs%2Fkit)。在 [Awesome Vite](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fgithub.com%2Fvitejs%2Fawesome-vite%23templates) 有一个由社区维护的模板列表。

#### **开发改进**

**Vite CLI**

```
**VITE** v3.0.0  ready in **320** ms

  **➜**  **Local**:   http://127.0.0.1:5173/
  **➜**  **Network**: use --host to expose
```
除了 CLI 的美学改进之外，默认的开发服务器端口现在是 5173，预览服务器监听端口是 4173。此更改将确保 Vite 将避免与其他工具发生冲突。

**改进的 WebSocket 连接策略**

Vite 2 的痛点之一是在代理后面运行时配置服务器。Vite 3 更改了默认的连接方案，因此它在大多数情况下都是开箱即用的。所有这些设置现在都通过 [`vite-setup-catalogue`](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fgithub.com%2Fsapphi-red%2Fvite-setup-catalogue)作为 Vite Ecosystem CI 的一部分进行测试。

**冷启动改进**

Vite 现在可以避免在冷启动期间，当插件在抓取初始静态导入的模块时注入导入时完全重新加载（[#8869](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Fissues%2F8869)）。

![](https://upload-images.jianshu.io/upload_images/10024246-340d176d92007371.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**import.meta.glob**

`import.meta.glob` 支持被重写。可阅读 [Glob Import Guide](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fguide%2Ffeatures.html%23glob-import) 中的新功能：

[多个模式](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fguide%2Ffeatures.html%23multiple-patterns)可以作为数组传递
`*import*.meta.glob(['./dir/*.js', './another/*.js'])` 

现在支持 [Negative Patterns](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fguide%2Ffeatures.html%23negative-patterns)（以 `!` 为前缀）以忽略某些特定文件
`*import*.meta.glob(['./dir/*.js', '!**/bar.js'])` 

可以指定 [Named Imports 以改进 tree-shaking](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fguide%2Ffeatures.html%23named-imports)
`*import*.meta.glob('./dir/*.js', { import: 'setup' })` 

可以通过[自定义查询以 attach metadata](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fguide%2Ffeatures.html%23custom-queries)
`*import*.meta.glob('./dir/*.js', { query: { custom: 'data' } })` 

[Eager Imports](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fguide%2Ffeatures.html%23glob-import) 现在作为一个 flag 传递
`*import*.meta.glob('./dir/*.js', { eager: true })` 

**使 WASM Import 与 Future Standards 保持一致**

WebAssembly Import API 已经过修订，以避免与 Future Standards 发生冲突并使其更加灵活：

```
import init from './example.wasm?init'

init().then((instance) => {
  instance.exports.test()
})

```

在 [WebAssembly guide](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fguide%2Ffeatures.html%23webassembly) 中了解更多信息。

#### **构建改进**

**ESM SSR 默认构建**

生态系统中的大多数 SSR 框架已经在使用 ESM 构建。因此，Vite 3 使 ESM 成为 SSR 构建的默认格式。这使得可以简化以前的 [SSR 外部化启发式方法](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fguide%2Fssr.html%23ssr-externals)，默认情况下外部化依赖项。

**改进的 Relative Base 支持**

Vite 3 现在正确支持 relative base（使用 `base: ''`），允许将构建的资产部署到不同的 bases 而无需重新构建。这在构建时不知道 base 的情况下非常有用，例如在部署到 [IPFS](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fipfs.io%2F) 等内容可寻址网络时。

#### **减少捆绑包大小**

Vite 捆绑了它的大部分依赖项，并尽可能地尝试使用现代轻量级替代方案。Vite 3 的发布大小比 V2 小了 30%。
![](https://upload-images.jianshu.io/upload_images/10024246-caa04adf46718cd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### **错误修复**

在过去的三个月里，Vite 的 open issues 从 770 个减少到了 400 个。

![](https://upload-images.jianshu.io/upload_images/10024246-671cbc784b619833.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-c85c778100f1a5aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### **兼容性说明**

*   Vite 不再支持已达到 EOL 的 Node.js 12。现在需要 Node.js 14.18+。
*   Vite 现在以 ESM 的形式发布，为了兼容，在 ESM entry 中加入了 CJS 代理。
*   Modern Browser Baseline 现在针对支持[原生 ES 模块](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fcaniuse.com%2Fes6-module)、[原生 ESM 动态导入](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fcaniuse.com%2Fes6-module-dynamic-import)和特性的浏览器以支持[原生 ES 模块](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fcaniuse.com%2Fes6-module)、[原生 ESM 动态导入](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fcaniuse.com%2Fes6-module-dynamic-import)和 [`import.meta`](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fcaniuse.com%2Fmdn-javascript_statements_import_meta)功能的浏览器为目标。
*   SSR 和 library 模式下的 JS 文件扩展名现在使用有效的扩展名（js、mjs 或 cjs）来输出基于其格式和包类型的 JS entries 和 chunks。

在[迁移指南](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fvitejs.dev%2Fguide%2Fmigration.html)中了解更多信息。
