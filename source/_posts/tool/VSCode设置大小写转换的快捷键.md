---
title: VSCode设置大小写转换的快捷键
date: 2021-03-10 21:12:40
categories: 开发工具
---
一 、默认情况下VS Code的编辑菜单或快捷键中没有 转换为大写，转换为小写的功能

解决方案1：使用键盘配置，开启转换为小写，转换为大写的快捷键 

以VS中的 快捷键为例：

小写 -》Ctrl+Shift+l

大写=》Ctrl+Shift+U

自定义大小写转换的快捷键步骤如下:

 1.点击【文件】-【首选项】-【键盘快捷方式】菜单。

![](https://upload-images.jianshu.io/upload_images/10024246-d5ee5749c318312e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

![](https://upload-images.jianshu.io/upload_images/10024246-423b8ab14aade804.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.输入 "转换为大写", 会出现搜索结果【转换为大写】,鼠标悬停在该结果上, 点击前面的"加号" 或 双击, 会弹出输入自定义快捷键的弹窗。

![](https://upload-images.jianshu.io/upload_images/10024246-60e4e6fcbe5fa6b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

![](https://upload-images.jianshu.io/upload_images/10024246-3f1c888971fb2a73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

3.在键盘上面按下Ctrl、Shift和u, 然后按Enter键, 即可成功绑定转换为大写快捷键。

![](https://upload-images.jianshu.io/upload_images/10024246-65a4b55675f8717b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

![](https://upload-images.jianshu.io/upload_images/10024246-9ecd4ce60718de32.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4.同理，搜索"转换为小写", 鼠标悬停在结果上, 点击前面的"加号" 或 双击, 弹出设置快捷键的弹窗，在键盘上面按下Ctrl、Shift和l, 然后按Enter键, 即可成功绑定转换为小写快捷键。

![](https://upload-images.jianshu.io/upload_images/10024246-7f3e695948c286af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


---
##解决方案2，直接修改keybings.json 添加配置

    {
        "key": "ctrl+shift+u",
        "command": "editor.action.transformToUppercase",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+l",
        "command": "editor.action.transformToLowercase",
        "when": "editorTextFocus"
    }
最终设置的自定义的大小写转换快捷键如下: 

```
转换为大写: Ctrl+Shift+u
转换为小写: Ctrl+Shift+l
```
