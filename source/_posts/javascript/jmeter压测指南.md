---
title: jmeter压测指南
date: 2022-04-03 16:28:03
categories: 前端
---
# 前言
你可能好奇，作为一个前端攻城狮，我为什么需要压测呢，这个和我有什么关系呢？如果你对自己的交付代码要求比较高，那么耐心的学一下，如果你想做个全栈，想写node服务，那么你肯定需要。
如果作为一个后端，那么你肯定也是需要的，你需要知道自己提供的接口是否有性能的问题，自己的代码是否健壮。
对于测试来说，肯定需要掌握的，原因就无需赘述了。
先写在前面，哪里写的不好，欢迎与各位老师沟通交流。

今天介绍的工具是jmeter，因为它免费而强大。

首先，压力测试是每一个Web应用程序上线之前都需要做的一个测试，通过压测可以帮助我们发现系统中的瓶颈问题，减少发布到生产环境后出问题的几率；预估系统的承载能力，使我们能根据其做出一些应对措施。所以压力测试是一个非常重要的步骤，现在为大家介绍一下我比较喜欢的jmeter。

# 关于jmeter

Apache JMeter是Apache组织开发的基于Java的压力测试工具。用于对软件做压力测试，它最初被设计用于Web应用测试，但后来扩展到其他测试领域。 它可以用于测试静态和动态资源，例如静态文件、Java 小服务程序、CGI 脚本、Java 对象、数据库、FTP 服务器， 等等。JMeter 可以用于对服务器、网络或对象模拟巨大的负载，来自不同压力类别下测试它们的强度和分析整体性能。另外，JMeter能够对应用程序做功能/回归测试，通过创建带有断言的脚本来验证你的程序返回了你期望的结果。为了最大限度的灵活性，JMeter允许使用正则表达式创建断言。
Apache jmeter 可以用于对静态的和动态的资源（文件，Servlet，Perl脚本，java 对象，数据库和查询，FTP服务器等等）的性能进行测试。它可以用于对服务器、网络或对象模拟繁重的负载来测试它们的强度或分析不同压力类型下的整体性能。你可以使用它做性能的图形分析或在大并发负载测试你的服务器/脚本/对象。

# 开始前的准备工作

jmeter是使用java开发的，所以[JDK](https://www.oracle.com/java/technologies/javase/jdk16-archive-downloads.html)是我们必须的，jdk在8版本以后就不需要配置环境变量了，所以关于环境变量的配置就不再赘述了。
java安装之后，通过javac -version查看版本来验证是否安装成功。
还有一个就是[jmeter](https://ttc.zhiyinlou.com/jmeter)了！上面的是可以直接使用的版本，下面的是源代码，可以进行二次开发，或者自己编译的。
![](https://upload-images.jianshu.io/upload_images/10024246-ddffe44161ab4993.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
解压之后，进去目录(jmeter/bin),执行sh jmeter就可以看到GUI了。

![](https://upload-images.jianshu.io/upload_images/10024246-56df67682907947c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-57ae77846a4c4688.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

命令行的消息为：

![](https://upload-images.jianshu.io/upload_images/10024246-0fb63236e71822b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

意思是：不要使用GUI运行压力测试，只使用GUI用于压力测试的创建和调试；执行压力测试请不要使用GUI。
可以更换为中文的语言：
![](https://upload-images.jianshu.io/upload_images/10024246-3019a4e562cdcbde.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

介绍一个奇淫巧技，这里的外观要选择，选择第一个，不要修改了，否则自己的设置无法保存。

![](https://upload-images.jianshu.io/upload_images/10024246-b1de204cd6a74745.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 开始创建

#### 第一步，创建线程组

![](https://upload-images.jianshu.io/upload_images/10024246-ceb1dd8cad6c5b51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里设置线程数和循环次数。我这里设置线程数为100，循环一次。
![](https://upload-images.jianshu.io/upload_images/10024246-772ed9d487fa03d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 第二步，创建配置元件，选择http默认值。

![](https://upload-images.jianshu.io/upload_images/10024246-5071a61e8501da71.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

配置我们需要进行测试的程序协议、地址和端口
![](https://upload-images.jianshu.io/upload_images/10024246-ea247cd4f4dd136d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 第三步，构造HTTP请求。

![](https://upload-images.jianshu.io/upload_images/10024246-0a614cfb151d852f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

配置我们需要进行测试的程序协议、地址和端口
![](https://upload-images.jianshu.io/upload_images/10024246-90d37ee76118abe0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 第四步，添加HTTP请求头。

在我们刚刚创建的线程组上右键 【添加】–>【配置元件】–>【HTTP信息头管理器】。
![image.png](https://upload-images.jianshu.io/upload_images/10024246-0c3e94a24dde7d87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 第五步，添加断言。

在我们刚刚创建的线程组上右键 【添加】–>【断言】–>【响应断言】。
![](https://upload-images.jianshu.io/upload_images/10024246-029e1b63d2390577.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

根据响应的数据来判断请求是否正常。我在这里只判断的响应代码是否为200。还可以配置错误信息。
![](https://upload-images.jianshu.io/upload_images/10024246-1a988b6962b78476.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 第六步，添加察看结果树。

在我们刚刚创建的线程组上右键 【添加】–>【监听器】–>【察看结果树】。
![](https://upload-images.jianshu.io/upload_images/10024246-0388404388f12e50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 第七步，创建汇总报告。

在我们刚刚创建的线程组上右键 【添加】–>【监听器】–>【汇总报告】。
![](https://upload-images.jianshu.io/upload_images/10024246-deed4de413dfb8ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 开始测试。
![](https://upload-images.jianshu.io/upload_images/10024246-c3cc17f80ffc81f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

汇总报告：
![](https://upload-images.jianshu.io/upload_images/10024246-d2d0db9aa1bb025b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

每个请求的详情：
![](https://upload-images.jianshu.io/upload_images/10024246-0b79a573a363b14f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# [](https://link.juejin.cn?target=)开始压测计划

在前面介绍了，我们不能直接通过gui直接测试，需要使用命令行。
命令为： jmeter -n -t [jmx file] -l [results file] -e -o [Path to web report folder]

jmx file是测试计划文件路径
results file是测试结果文件路径
Path to web report folder 报告路径
![](https://upload-images.jianshu.io/upload_images/10024246-76bd88023e1fe9cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行完毕之后，从“Path to web report folder”的报告路径，查看汇总结果，打开html既可：
![](https://upload-images.jianshu.io/upload_images/10024246-66e1494965e1539d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>作者：uniqueli
链接：https://juejin.cn/post/7047754943637225480
