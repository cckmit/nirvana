---
title: macOS系统下安装Homebrew慢
date: 2021-03-10 21:12:40
categories: mac
---
#### 1.获取install文件并编辑

```
cd ~
curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install >> brew_install
```

> 编辑的话直接在Finder里面搜索brew_install，双击之后打开，一般会用**Xcode**打开。原文说的“**注释掉BREW_REPO = "https://github.com/Homebrew/brew".freeze和CORE_TAP_REPO = "https://github.com/Homebrew/homebrew-core".freeze**”，新版没有**CORE_TAP_REPO**这一行，不过没关系，新增上去就行了。

**修改后代码和截图如下**

```
#!/usr/bin/ruby
# This script installs to /usr/local only. To install elsewhere (which is
# unsupported) you can untar https://github.com/Homebrew/brew/tarball/master
# anywhere you like.
HOMEBREW_PREFIX = "/usr/local".freeze
HOMEBREW_REPOSITORY = "/usr/local/Homebrew".freeze
HOMEBREW_CORE_TAP = "/usr/local/Homebrew/Library/Taps/homebrew/homebrew-core".freeze
HOMEBREW_CACHE = "#{ENV["HOME"]}/Library/Caches/Homebrew".freeze
#BREW_REPO = "https://github.com/Homebrew/brew".freeze#修改前
BREW_REPO = "git://mirrors.ustc.edu.cn/brew.git".freeze#修改后
CORE_TAP_REPO = "git://mirrors.ustc.edu.cn/homebrew-core.git".freeze#新增
```

![](https://upload-images.jianshu.io/upload_images/10024246-03688f54a0acd5dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 2.开始安装

```
/usr/bin/ruby ~/brew_install
```

> 在这里我没有遇到原文下面提问的朋友遇到的卡住报错不动等问题，仅停顿了一会儿。

#### 3.替换源

```
#替换homebrew默认源
cd "$(brew --repo)"
git remote set-url origin git://mirrors.ustc.edu.cn/brew.git

#替换homebrew-core源
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin git://mirrors.ustc.edu.cn/homebrew-core.git

```

#### 4.brew更新

```
brew update
```

**最后:**

```
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
```
