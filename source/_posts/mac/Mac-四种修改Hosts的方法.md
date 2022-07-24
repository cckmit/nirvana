---
title: Mac-四种修改Hosts的方法
date: 2021-03-10 21:12:40
categories: mac
---
　　1、通过 VI 编辑器修改

　　打开终端（应用程序——实用工具），运行：

　　sudo vi /etc/hosts

![image](https://upload-images.jianshu.io/upload_images/10024246-67eb013c22cf36b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　屏幕上会提示你输入密码（输入密码的时候不会有任何字符显示，甚至*都不会显示，输完之后按回车就是了），打开 hosts 文件之后按 i 键进入插入模式（可理解为编辑模式，如下图所示，会有「INSERT 」提示，即可插入编辑的意思），然后按照你的需要对该文件进行编辑，编辑完成之后按 ESC 键退出插入模式，之后按「 :wq+回车」保存退出，记得英文的冒号也是要输入的哦。

![image](https://upload-images.jianshu.io/upload_images/10024246-3e19c469c281313b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　VI 编辑器对于经常使用 Linux 的用户应该不会陌生，而且该编辑器是默认内置在 Mac 系统中的。但是，对于没有使用过 VI 的用户，还真得花一会儿功夫来学习其基本使用方法。另外VI编辑器是一个很好很强大的工具，想要玩好 Mac 的话，最好掌握这个工具的使用。

　　2、直接在 Mac 系统下找到 Hosts 文件修改

　　这应该算是最简单以及最直观的一个修改方法了。

　　第一步、打开你的文件管理器也就是Finder ，Hosts自然也是在这个里面。

![image](https://upload-images.jianshu.io/upload_images/10024246-7bf8749a01353aac.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　第二步、接下来请按快捷键组合 Shift+Command+G 三个组合按键查找文件，并输入 Hosts 文件的所在路径：/etc/hosts ，如图所示：

![image](https://upload-images.jianshu.io/upload_images/10024246-8af91772eae318b4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　第三步、在打开的文件夹当中找到「Hosts」文件夹。

![image](https://upload-images.jianshu.io/upload_images/10024246-d249d67354796e5f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　第四步、复制这个「hosts」文件到桌面上，鼠标右键点击/右击它，选择「打开方式」—「文本编辑」，打开这个「hosts」文件，然后将你要修改的内容直接在里面修改就好了。

![image](https://upload-images.jianshu.io/upload_images/10024246-a60eca4f5ebbdbe7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　第五步、将你修改好的 hosts 文件在复制回 Hosts 文件的所在（文件夹下）路径：/etc/hosts 之下替换原始的 hosts 文件就好了，这样你 Mac 系统下的 hosts 文件就被成功修改了。

![image](https://upload-images.jianshu.io/upload_images/10024246-7f4fe79c935b1b22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　3、使用 cat 命令合并文件

　　这个方法比较适合当你想往 Hosts 文件中添加一些内容的情况，首先你需要把需要添加到 Hosts 文件中的内容保存为一个TXT文本文件（建议直接保存在你的用户目录），我这里拿保存在用户目录中的123.txt为例说明。

　　同样是启动终端，然后运行下面两条命令：

　　sudo -s

　　cat ~/123.txt/etc/hosts

　　~ 符号在 Mac 甚至所有基于 Unix 和 Linux 的系统中都是代表当前用户的用户目录，.代表当前目录，这回你就应该明白上面的命令中为什么是 ~/123.txt 了吧。

　　4、直接在图形界面中修改

　　打开 Finder，然后点击菜单栏中的 前往——前往文件夹（或者快捷键 Shift+Command+G），在路径中输入 /private，进入之后在 etc 文件夹上点击右键——显示简介，在文件夹简介窗口的最下面找到“共享与权限”，将 everyone 的权限修改为“读与写”，如果你发现不能修改的话，将右下角的那把小锁解开就可以修改了。

![image](https://upload-images.jianshu.io/upload_images/10024246-0847ef6d184c1f5d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　修改 etc 文件夹的权限之后，再进入 etc 文件夹下面，修改名为 hosts 文件的权限（同样是everyone读与写），修改完成之后，你就可以直接在 hosts 文件上点右键，通过“文本编辑”打开并编辑该文件了，不会出现没有权限的提示。修改并保存完成之后，记得将该文件和 etc 文件夹的权限还原。
