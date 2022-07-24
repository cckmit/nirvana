---
title: git代码统计
date: 2021-03-09 21:12:40
categories: git
---
## 命令行

### 查看git上的个人代码量：

```
git log --author="username" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -
```

结果示例：(记得修改 username)

```
added lines: 120745, removed lines: 71738, total lines: 49007
```

### 统计每个人增删行数

```
git log --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done
```

结果示例


![](https://upload-images.jianshu.io/upload_images/10024246-9d65172280d94945.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###统计时间段每个人增删行数

```
 git log  --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat:  --since ==2020-1-1 --until=2020-2-15 --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done
```

### 查看仓库提交者排名前 5

```
git log --pretty='%aN' | sort | uniq -c | sort -k1 -n -r | head -n 5
```

### 贡献值统计

```
git log --pretty='%aN' | sort -u | wc -l
```

### 提交数统计

```
git log --oneline | wc -l
```

### 添加或修改的代码行数：

```
git log --stat|perl -ne 'END { print $c } $c += $1 if /(\d+) insertions/'
```

## 使用gitstats

[GitStats项目](https://github.com/hoxu/gitstats)，用Python开发的一个工具，通过封装Git命令来实现统计出来代码情况并且生成可浏览的网页。官方文档可以参考这里。

### 使用方法

```
git clone git://github.com/hoxu/gitstats.git
cd gitstats
./gitstats 你的项目的位置 生成统计的文件夹位置
```
可能会提示(我的为 mac os)
```
env: python2: No such file or directory
```
可以进行 python2 的链接
```
sudo ln -sf /usr/bin/python2.7 /usr/local/bin/python2
```

没有安装gnuplot画图程序，那么需要安装再执行：

```
//mac osx
brew install gnuplot
//centos linux
yum install gnuplot
```

生成的统计文件为HTML：
![](https://upload-images.jianshu.io/upload_images/10024246-194e0a1a7127a69f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

