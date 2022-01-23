---
title: Vue3即将转正迎来春天
date: 2022-01-22 16:28:03
category: news
---
Vue 3 将在 2022 年 2 月 7 日成为新的默认版本！
## 版本切换细节

下面是我们所说的“新的默认版本”的具体细节。此外，请阅读可能需要采取的措施部分，来确认你是否需要在默认版本切换之前做相应改动以避免发生异常。

##### npm 发布标签

*   `npm install vue` 将默认安装 Vue 3。

*   所有其他官方 npm 包的 `latest` 发布标签将指向其 Vue 3 的兼容版本，包括 `vue-router`、`vuex`、`vue-loader` 和 `@vue/test-utils`。

##### 官方文档与站点

所有的文档和官方站点将默认切换到 Vue 3 版本。包括：

*   vuejs.org
*   router.vuejs.org
*   vuex.vuejs.org
*   vue-test-utils.vuejs.org (将迁移到 test-utils.vuejs.org)
*   template-explorer.vuejs.org

请注意，新的 vuejs.org 将是[完全重写的版本](https://staging.vuejs.org)，而不是目前部署在 v3.vuejs.org 的版本。

这些站点当前的 Vue 2 版本将被迁移到新地址 (版本前缀表示库的各自版本，而非 Vue 核心库的版本)：

*   vuejs.org -> v2.vuejs.org (旧的 v2 网址将自动重定向到新地址上)
*   router.vuejs.org -> v3.router.vuejs.org
*   vuex.vuejs.org -> v3.vuex.vuejs.org
*   vue-test-utils.vuejs.org -> v1.test-utils.vuejs.org
*   template-explorer.vuejs.org -> v2.template-explorer.vuejs.org
## Vue3的6个优势
- 1.Vue3使用TS重构了项目，获得更好的类型支持
- 2. 重构了响应式系统
Vue3使用Proxy替换Object.defineProperty，重构了Vue的响应式系统；解决了Vue2.x中存在的响应性问题
包括：
可的直接监听数组类型的数据变化
监听的目标为对象本身，不需要像Object.defineProperty一样遍历每个属性，有一定的性能提升
更强大的拦截功能，拦截器包括get、set、has、deleteProperty、ownKeys、getOwnPropertyDescriptor、defineProperty、preventExtensions、getPrototypeOf、isExtensible、setPrototypeOf、apply、construct13种

- 3. 引入了Composition API
vue3.0中引入了composition API，专门用于解决功能、数据和业务逻辑分散的问题，使项目更益于模块化开发以及后期维护
它的核心是setup函数，这样做的好处是，当应用变得复杂一点时，我可以将功能对应的数据和业务逻辑抽离出来，需要是import，以至于更好的逻辑复用和代码组织
- 4.  优化了Virtual DOM
包括：
模板编译时的优化；将一些静态节点编译成常量
slot优化；将slot编译为lazy函数，将slot的渲染的决定权交给子组件
模板中内联事件的提取并重用（原本每次渲染都重新生成内联函数）

- 5.  更好的Tree shaking
引入尤大的原话：`Tree-shaking其实就是把无用的模块进行“剪枝”，很多没有用到的API就不会打包到最后的包里`
事实上，Tree shaking并不是Vue3新增的东西，它是打包工具如webpack或者rollup的功能，但是由于Vue3代码结构调整，把vue本身当一个对象去操作，这样的结果是，使得一些可能不会用到的功能就可以被tree shaking掉，从而使得体积更小
主要原理：依赖es6的模块化的语法，将无用的代码(dead-code)进行剔除
- 6. `script setup` 
`script setup`是Vue3.2新增的语法糖，消除我们在使用compositionAPI时的心智负担，让我们没有理由不使用compositionAPI
优势：
更简洁
能够使用纯TS声明props和抛出事件
更好的运行时性能和IDE类型推断性能

以上就是对比Vue2，Vue3的几个优势

