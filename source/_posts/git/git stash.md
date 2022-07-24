---
title: git stash
date: 2021-10-06 21:19:18
categories: git
---
>当你使用 git 正在开发一个功能的时候，如果你突然需要到另一个分支去开发却不想放弃当前的改动的时候，你可以使用 git stash

##### 命令
`git stash list`
列出所有储藏

`git stash show [<stash>:Number]`
显示某一个（默认最近一个）储藏详情

`git stash drop [-q|--quiet][<stash>]`
删除某一个（默认最近一个）储藏

`git stash ( pop | apply ) [--index][-q|--quiet] [<stash>]`
恢复储藏并删除 (pop) / 不删除 (apply) 恢复的储藏

`git stash branch <branchname> [<stash>]`
从储藏创建分支

`git stash [push [-p|--patch]-k|--[no-]keep-index] [-q|--quiet] [-u|--include-untracked] [-a|--all] [-m|--message <message>] [--] [<pathspec>…]]`
储藏，但默认不会储藏未跟踪的文件和被忽略的文件

`git stash clear`
删除所有储藏

`git stash create [<message>]`
创建一个悬空提交 (dangling commit)，不会将 ref 存储在任何地方，使用 git stash store 保存它

`git stash store [-m|--message <message>][-q|--quiet] <commit>`
存储上一个命令中创建的悬空提交

参数
- `q|--quiet` 静默模式
- `p|--patch` 以 patch 方式 push stash
- `k|--[no-]keep-index` 保留 index 序号
- `u|--include-untracked` untracked 状态的文件也会被 push
- `a|--all` `untracked` 和` ignored` 的文件也会被 push
- `m|--message` `<message>` 输出 stash 信息
- `-- [<pathspec>]` 针对特定的路径 push
