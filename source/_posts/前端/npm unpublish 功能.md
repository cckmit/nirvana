---
title: npm unpublish 功能
date: 2022-01-25 16:28:03
category: javascript
---
## npm unpublish 功能

从源站上中删除软件包

## npm unpublish 使用

`npm unpublish [<@scope>/]<pkg>[@<version>]`

## npm unpublish 警告

通常，删除其他人依赖的库版本是不良行为！

`deprecate `如果您的目的是鼓励用户升级，请考虑使用该命令。

注册表上有足够的空间。

## npm unpublish 说明

这将从源站中删除软件包版本，删除其条目并删除压缩包。

如果未指定任何版本，或者如果删除了所有版本，则根包条目将从注册表中完全删除。

即使未发布软件包版本，该特定名称和版本组合也永远无法重用。

为了再次发布该程序包，必须使用新的版本号。此外，未发布每个版本的软件包的新版本可能要等到 24 小时后才能重新发布。

使用默认源站（`registry.npmjs.org`），仅允许最近 72 小时内发布的版本取消发布。