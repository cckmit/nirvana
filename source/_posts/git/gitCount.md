---
title: Git统计每个人提交的Commit次数和代码行数
date: 2021-03-09 20:35:36
categories: git
---
提交次```git shortlog --numbered --summary```
查看所有的```commit数git log --oneline | wc -l```
提交删除行数```git log --author="$(git config --get user.name)" --pretty=tformat: --numstat | awk '{ add += $1 ; subs += $2 ; loc += $1 - $2 } END { printf "added lines: %s removed lines : %s total lines: %s\n",add,subs,loc }'```
使用cloc统计代码行数给力：
安装```brew install cloc```
统计目录下的所有文件，代码行数```cloc ./```
排除文件夹```cloc ./ --exclude-dir=Pods,Tests,Utility```