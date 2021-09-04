---
title: Git-提交规范
date: 2021-09-09 21:12:40
category: git
---
git是现在市面上最流行的版本控制工具，书写良好的commit message能大大提高代码维护的效率。但是在日常开发中由于缺少对于commit message的约束，导致填写内容随意、质量参差不齐，可读性低亦难以维护。在项目中引入commit message规范已是迫在眉睫。

## 用什么规范？

现在市面上比较流行的方案是约定式提交规范（Conventional Commits），它受到了Angular提交准则的启发，并在很大程度上以其为依据。约定式提交规范是一种基于提交消息的轻量级约定。它提供了一组用于创建清晰的提交历史的简单规则；这使得编写基于规范的自动化工具变得更容易。这个约定与SemVer相吻合，在提交信息中描述新特性、bug 修复和破坏性变更。它的 message 格式如下:
```
<类型>[可选的作用域]: <描述>

[可选的正文]

[可选的脚注]
```

## Quick Start

##### 1.全局安装commitizen & cz-conventional-changelog

commitizen是一个撰写合格commit message的工具，用于代替git commit 指令，而cz-conventional-changelog适配器提供conventional-changelog标准（约定式提交标准）。基于不同需求，也可以使用不同适配器。
```
npm install -g commitizen cz-conventional-changelog
echo '{ "path": "cz-conventional-changelog" }' > ~/.czrc
```
安装完毕后，可直接使用git cz来取代git commit。

全局模式下，需要 ~/.czrc 配置文件, 为commitizen指定Adapter。

##### 2.项目内安装commitlint & husky

commitlint负责用于对commit message进行格式校验，husky负责提供更易用的git hook。

```
Use npm
npm i -D husky @commitlint/config-conventional @commitlint/cli

Use yarn
yarn add husky @commitlint/config-conventional @commitlint/cli -D
```
commitlint只能做格式规范，无法触及内容。对于内容质量的把控只能靠我们自己。

###### 3\. 添加相应配置

创建commitlint.config.js

```
# In the same path as package.json

echo 'module.exports = {extends: ["@commitlint/config-conventional"]};' > ./commitlint.config.js

引入husky

# package.json

...,
"husky": {
    "hooks": {
      "commit-msg": "commitlint -e $GIT_PARAMS"
    }
}
```
##### 4\. 使用

执行git cz进入interactive模式，根据提示依次填写

```
1.Select the type of change that you're committing 选择改动类型 (<type>)

2.What is the scope of this change (e.g. component or file name)? 填写改动范围 (<scope>)

3.Write a short, imperative tense description of the change: 写一个精简的描述 (<subject>)

4.Provide a longer description of the change: (press enter to skip) 对于改动写一段长描述 (<body>)

5.Are there any breaking changes? (y/n) 是破坏性修改吗？默认n (<footer>)

6.Does this change affect any openreve issues? (y/n) 改动修复了哪个问题？默认n (<footer>)
```

生成的commit message格式如下：

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```
填写完毕后，husky会调用commitlint对message进行格式校验，默认规定type及subject为必填项。

任何git commit指令的option都能用在 git cz指令上, 例如git cz -a

## Commit message规范在rrd-fe落地使用情况

针对团队目前使用的情况，我们讨论后拟定了commit message每一部分的填写规则。

##### 1\. type

type为必填项，用于指定commit的类型，约定了feat、fix两个主要type，以及docs、style、build、refactor、revert五个特殊type，其余type暂不使用。

```
# 主要type
feat:     增加新功能
fix:      修复bug

# 特殊type
docs:     只改动了文档相关的内容
style:    不影响代码含义的改动，例如去掉空格、改变缩进、增删分号
build:    构造工具的或者外部依赖的改动，例如webpack，npm
refactor: 代码重构时使用
revert:   执行git revert打印的message

# 暂不使用type
test:     添加测试或者修改现有测试
perf:     提高性能的改动
ci:       与CI（持续集成服务）有关的改动
chore:    不修改src或者test的其余修改，例如构建过程或辅助工具的变动
```

当一次改动包括主要type与特殊type时，统一采用主要type。

##### 2\. scope

scope也为必填项，用于描述改动的范围，格式为项目名/模块名，例如：node-pc/common rrd-h5/activity，而we-sdk不需指定模块名。如果一次commit修改多个模块，建议拆分成多次commit，以便更好追踪和维护。

##### 3\. body

body填写详细描述，主要描述改动之前的情况及修改动机，对于小的修改不作要求，但是重大需求、更新等必须添加body来作说明。

##### 4\. break changes

break changes指明是否产生了破坏性修改，涉及break changes的改动必须指明该项，类似版本升级、接口参数减少、接口删除、迁移等。

##### 5\. affect issues

affect issues指明是否影响了某个问题。例如我们使用jira时，我们在commit message中可以填写其影响的JIRA_ID，若要开启该功能需要先打通jira与gitlab。参考文档：docs.gitlab.com/ee/user/pro…

填写方式例如：

```
re #JIRA_ID
fix #JIRA_ID
```

## 示例

*   完整的commit message示例

![](https://upload-images.jianshu.io/upload_images/10024246-b50d7aff2010a341.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


*   相应的git log

![](https://upload-images.jianshu.io/upload_images/10024246-70368a01a49c55b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

