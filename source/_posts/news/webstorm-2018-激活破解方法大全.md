---
title: webstorm 2018 激活破解方法大全
date: 2021-03-11 21:12:40
categories: 资讯
---

**方法一：（更新时间：2018/4/8）v3.3**

* * *

注册时，在打开的License Activation窗口中选择“License server”，在输入框输入下面的网址：

[http://idea.wrbugtest.tk/](http://idea.wrbugtest.tk/) (2018/06/16)

点击：Activate即可。

网友分享 : [感谢 weixin_41405655 ]

```
===== LICENSE BEGIN =====
37362-12042010
00000!enirob"h4FBnAgcpdNnIZydA
9AikY7i7Ecn7GW7EvybU"YwuAPkdCw
Qn7Fp!9FpNvujTEghtbTGz1DutM216
===== LICENSE END =====
```

复制license begin和license end 之间的一段代码可以用

**方法二：获取注册码**

* * *

打开网址（[IntelliJ IDEA 注册码](http://idea.lanyus.com/)），我们能看到下面的界面，直接点击获取激活码，将生成的激活码粘贴到WebStorm激活对话框中的Lisence Code输入框，点击OK即可破解。

![](https://upload-images.jianshu.io/upload_images/10024246-f51a26fb0fcc173e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](https://upload-images.jianshu.io/upload_images/10024246-e3b97f62ba6355d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**方法三：使用破解补丁**

* * *

首先下载补丁，看图！

2017版下载地址 : 链接：[https://pan.baidu.com/s/1Ed2kNzmGNrU5AsXbC2LkEw](https://pan.baidu.com/s/1Ed2kNzmGNrU5AsXbC2LkEw) 密码：465h

2018版下载地址 : 链接：[https://pan.baidu.com/s/1TZ_kXvkgF2t3hKusQl5TDQ](https://pan.baidu.com/s/1TZ_kXvkgF2t3hKusQl5TDQ) 密码：vc39

![](https://upload-images.jianshu.io/upload_images/10024246-808ac27a6bfb9377.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


然后将补丁复制到安装目录的bin目录下

```
D:\...\WebStorm\bin
```

PS：一定要把这个jar包复制到这个bin目录，不然后面操作可能无法进行。

修改同目录下的 WebStorm.exe.vmoptions 和WebStorm64.exe.vmoptions，这两个文件一个是32位的，一个是64位的，建议同步修改。

用文本编辑器打开之后，在文件最上面加一行代码 ：

```
-javaagent:D:/Program Files (x86)/JetBrains/WebStorm/bin/破解补丁名字.jar
```

![这里写图片描述](http://upload-images.jianshu.io/upload_images/10024246-b1701a3f06a92ab8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
配置好之后，保存文件。在此再次启动WebStorm，就会看到下面所示界面，至此补丁激活就成功了。

```

![这里写图片描述](http://upload-images.jianshu.io/upload_images/10024246-d41d37b270730a54?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**方法四:自己搭建IntelliJ IDEA授权服务器**

* * *

这个比较繁琐,喜欢折腾的人可以试试:

IntelliJ IDEA License Server本地搭建教程

[ilanyu’s大牛的博客里有详细的步骤](http://blog.lanyus.com/archives/174.html)

如果有更好的方法,欢迎留言交流。
