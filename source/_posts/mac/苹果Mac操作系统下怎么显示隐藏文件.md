---
title: 苹果Mac操作系统下怎么显示隐藏文件
date: 2021-03-10 21:12:40
categories: mac
---
方法一：
第一步：打开「终端」应用程序。
第二步：输入如下命令：
```defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder```
第三步：按下「Return」键确认。
现在你将会在 Finder 窗口中看到那些隐藏的文件和文件夹了。
如果你想再次隐藏原本的隐藏文件和文件夹的话，将上述命令替换成
```defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder```
即可。
方法二：
Finder界面是，最上方，通过“前往”进入“电脑”或文件夹，先进入到需要显示隐藏文件的文件夹下
接着按```Command+F```,在窗格上会显示搜索栏
然后将第一个下列选择项“种类kind”选择为“其它Other”，当选择“其它”时，弹出新的搜索窗口，找到下面的“文件不可见File invisible”项，勾上后面的对勾，再单击“好OK”即可，返回文件夹，就可以看到，隐藏的文件已经显示出来了
