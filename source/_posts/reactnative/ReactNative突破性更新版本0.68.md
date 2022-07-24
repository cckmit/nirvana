---
title: ReactNative突破性更新版本0.68
date: 2022-04-09 21:12:40
categories: 
    - [react-native]
    - [资讯]
tags: [react-native]
---
## **亮点**

### **重大更新**

*   ReactNative升级Node 16, 此更改意味着用户现在需要使用 Node >= 14 的版本
*   Android Gradle插件升级至7.0.1，强制使用JDK 11进行Android build
*   移除了iOS ApiRCTBundleURLProvider中的fallbackResource

相关工具类的更新：

*   @react-native-community/cli to 7.0.3
*   Metro to 0.67
*   react-devtools-core dependency to 4.23.0
*   Flipper to 0.125.0
*   react-native-codegen to 0.0.9
*   Kotlin to 1.6.10
*   Soloader to 0.10.3
*   Gradle to 7.3
*   Android compile and target SDK to 31

### **其他更新和修复**

## **可选的新架构**

React Native 0.68是第一个对Fabric Renderer和TurboModule系统提供支持的版本。这标志着新React Native架构推广的一个重要里程碑。为了帮助你尽快了解这些变化，我们在**[网站](https://link.zhihu.com/?target=https%3A//reactnative.dev/architecture/overview)**上增加了架构部分，在那里你可以找到几个关于新系统内部的深度指南。

同时，我们在文档中增加了**[迁移指南](https://reactnative.dev/docs/next/new-architecture-intro)**，并启动了一个专门针对新架构的工作组。你可以在之前的**[博文](https://reactnative.dev/blog/2022/03/15/an-update-on-the-new-architecture-rollout)**中找到更多信息，包括如何选择加入.

值得注意的是，新架构仍然需要进行一些微调。一些你依赖的第三方库可能还没有被迁移，你可能会遇到我们还没有发现的问题。

关于React 18：React Native 0.68 不支持 React 18 的新渲染引擎，这将在未来的版本中进行支持。

## **官方网站的更新**

ReactNative**[官方网站](https://reactnative.dev/)**做了一些调整，增加了架构说明和开发人员的贡献内容。
