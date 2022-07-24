---
title: 使用Whistle调试Web
date: 2021-06-10 16:36:28
categories: 前端
---
[Whistle](https://link.zhihu.com/?target=https%3A//github.com/avwo/whistle) 是一款 Web 调试工具，使用 Whistle，可以修改一个 HTTP/HTTPS 请求的方方面面，我们可以通过它 Mock 数据、修改请求延迟时间、修改静态文件等等。市面上有许多 Web 调试工具，比如在 Windows 上比较有名的 Fiddler，比如说 Charles 等等。而 Whistle 具有开源免费、配置简单、以及跨平台等特点，在使用它一段时间之后，我认为在前端开发中它完全能够代替上述的工具。下面让我们结合实际场景来看看 Whistle 能帮助我们做什么吧。

## 快速起步

在开始使用 Whistle 之前，需要安装以及进行一些配置。

```
# 1\. 下载
npm i -g whistle
# 2\. 启动 whistle 默认监听在 8899 端口
w2 start
```

启动 Whistle 之后，我们访问 `localhost:8899` 就可以看到 Whistle 的配置界面了，如图，界面左侧有 4 个菜单项，Network 可以查看请求响应的详情。Rules 是规则配置的地方，关于规则是什么，我们放到后面讲。在 Values 中我们可以配置 key-value 数据，之后可以在 Rules 使用对应的键值进行引用。Plugins 里展示了我们额外安装的 Whistle 插件，并且可以对这些插件进行配置。

![](https://upload-images.jianshu.io/upload_images/10024246-3b8f23d096a03b1c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Whistle 的规则主要由两部分组成，pattern 和 operatorURI，Whistle 使用 pattern 对请求进行匹配，并对匹配上的请求上应用 operatorURI 定义的操作，通过不同的 operatorURI，可以做到替换请求体、响应体、响应码等等事情。

![](https://upload-images.jianshu.io/upload_images/10024246-692ec2b174912915.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了让我们的请求被 Whistle 代理，还需配置 HTTP 代理或者 HTTPS 代理到 Whistle，如果使用 Chrome 浏览器，推荐 [SwitchyOmega](https://link.zhihu.com/?target=https%3A//chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif%3Fhl%3Dzh-CN) 配置代理，这样更加灵活。另外，为了能够抓取 HTTPS 协议的包，我们还需要下载[安装并信任 Whistle 的 HTTPS 证书](https://link.zhihu.com/?target=https%3A//wproxy.org/whistle/webui/https.html)，其中，关于 iOS 的证书配置，我之前写了一篇

关于 Whistle 更加详细的配置方法，请参考 Whistle 的[官方文档](https://link.zhihu.com/?target=https%3A//wproxy.org/whistle/)。

下面，让我们看看 Whistle 的一些功能。

## 映射

```
example.com localhost:8000
```

通过配置这样的规则，当我们访问 `example.com` 的时候，实际请求会打到 `localhost:8000`。

如果我们的 API 是同源的，并且我们只想将静态资源的请求替换为本地开发中的，还可以这样配置：

```
# 假设所有 API 路径为 /api，仍然走实际地址
example.com/api example.com/api
# 其他请求到本地
example.com localhost:8000
```

## Mock 数据

日常开发中，我们经常需要对后端接口进行 Mock，这样就不需要依赖后端实际部署情况了，或者我们需要测试一些边缘情况，Mock 这些边缘情况就可以免除为测试这样的边缘链路需要做的许多麻烦事情。最简单的 Mock 可以针对一个 pattern 替换响应体和响应码等等。如下所示：

```
# Rules
# 将 example.com/api/user 接口的响应内容替换为 user.json 中的内容，并将响应码替换成 200
# 其中，user.json 在 Values 面板中保存
example.com/api/user resBody://{user.json} replaceStatus://200
```

我们甚至可以设定请求的响应延时，只需要再加一个 `resDelay` 的 operatorURI：

```
# 10s 后返回
example.com/api/user resBody://{user.json} replaceStatus://200 resDelay://10000
```

可能有时候会有更加复杂的 Mock 需求，比如几个接口之间互相有关联，需要跑通一个完整的链路可能需要一些定制的逻辑。Whistle 提供了很多插件，其中有一个能够提供非常强大 Mock 能力的插件——[whistle.vase](https://link.zhihu.com/?target=https%3A//github.com/whistle-plugins/whistle.vase)。使用这个插件，我们甚至可以直接写 JS 代码，根据我们的具体请求参数作出各种具体逻辑。

```
npm i -g whistle.vase
```

在安装完之后，就可以在 Plugins 中对其进行配置了。

## 移动端调试

移动端最佳的调试方案应该是直接连接到桌面浏览器的调试工具，比如说移动端的 Safari 可以直接连接到 Safari 上，安卓的 Chrome 可以直接连接到 Chrome 的 DevTools 上，这种调试方法所得到的调试功能最多最强大。但是，并不是所有情况下我们都可以使用这种方式进行调试的，此时就可以使用更加通用的方法，下面介绍两种借助 Whistle 进行移动端调试的方案。

Whistle 自带了 [Weinre](https://link.zhihu.com/?target=https%3A//github.com/nupthale/weinre)，可以进行移动端远程调试，开启该功能很简单，只需在需要调试的 pattern 后面加上如下operatorURI：

```
www.zhihu.com weinre://debug
```

其中 `debug` 为类别（可以自由指定名称），方便我们对各个页面进行分类，这样一来，我们在 Whislte 的应用界面的 Weinre 菜单中就可以看到 `debug` 了，点击 `debug`，在手机上打开对应的页面，就可以愉快地进行调试了。

另一种方案是使用移动端的调试面板（引入特定的包），目前有许多移动端的调试面板工具可以满足轻量级的调试需求，比如 [vConsole](https://link.zhihu.com/?target=https%3A//github.com/Tencent/vConsole)、[Eruda](https://link.zhihu.com/?target=https%3A//github.com/liriliri/eruda) 等等。不过，我们的代码中不一定有加载这些调试包（比如我们生产环境代码），我们可以借助 Whistle 在 HTML 响应体后面增加一段加载移动端调试面板的代码。比如说我们使用 Eruda，那么我们可以在 Values 中增加下面一段代码，取名为 load_eruda.js：

```
(function() {
  var script = document.createElement('script');
  script.onload = function() {
    eruda.init();
  };
  script.src = 'https://cdn.jsdelivr.net/npm/eruda';
  document.body.appendChild(script);
})()

```

假设我们要调试知乎（[http://www.zhihu.com](http://www.zhihu.com/)），利用 `jsAppend` 将这段自定义脚本加到 HTML 后方，像这样增加规则：

```
www.zhihu.com jsAppend://{load_eruda.js}
```

打开 `www.zhihu.com`，就可以看到 Eruda 的调试按钮了。

## 总结

以上就是对 Whistle 的介绍，Whistle 提供了超过 60+ 个协议（operatorURI），可以满足我们平时在 Web 调试过程中各式各样的需求，可以查看 Whistle 的文档进一步探索。当然，目前 Whistle 也存在一些不足，比如现在还不支持 HTTP/2 的抓包（作者有计划支持），比如 Mock 数据如何与团队共享的问题。不过，作为一款在积极开发且功能在我看来已经比较完善的应用，非常推荐大家一试。

## 附：其他类似工具

1.  [Wireshark](https://link.zhihu.com/?target=https%3A//www.wireshark.org/) - 网络数据包分析软件，网络协议栈的各个层的数据都可以很方便地查看。
2.  [Termshark](https://link.zhihu.com/?target=https%3A//termshark.io/) - 「终端版」的 Wireshark
3.  [Charles](https://link.zhihu.com/?target=https%3A//www.charlesproxy.com/) - 一款老牌 HTTP/HTTPS 抓包调试工具
4.  [Fiddler](https://link.zhihu.com/?target=https%3A//www.telerik.com/fiddler) - 又一款免费的 Web 调试工具
5.  [mitmproxy](https://link.zhihu.com/?target=https%3A//mitmproxy.org/) - 一款开源免费的交互式的 HTTPS 代理
