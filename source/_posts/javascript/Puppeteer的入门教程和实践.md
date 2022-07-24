---
title: Puppeteer的入门教程和实践
date: 2022-06-06 16:28:03
categories: 前端
---
Puppeteer是谷歌官方出品的一个通过DevTools协议控制headless Chrome的Node库。可以通过Puppeteer的提供的api直接控制Chrome模拟大部分用户操作来进行UI Test或者作为爬虫访问页面来收集数据。

在Puppeteer出现之前，测试web应用程序通常依靠两种方式：有头浏览器，和构建在JavaScript引擎之上的无头浏览器。无头浏览器意味着没有用户界面，有头浏览器是最终用户与产品进行交互的窗口。

这就造成了一种必须权衡的局面：速度（无头）与可靠性（有头）。Puppeter的设计目标是消除这种权衡，能够让开发人员利用Chromium浏览器环境来运行测试，并让他们能够灵活地利用有头与无头浏览器来实现用户场景的测试。

Puppeteer也非常适合开发人员，开发人员可以基于熟悉的测试框架集成Puppeteer来实现、拓展自己的测试能力。

简单来说使用Puppeteer，我们可以实现：

- 快速爬取网页内容
- 自动完成表单数据提交
- 实现在浏览器上的所有自动化操作
- 跟踪并记录页面加载性能
- 浏览器屏幕截图
- 实现自动化测试
- 从网页生成PDF
#### 环境和安装

Puppeteer因为是一个npm的包，所以安装很简单：

> npm i puppeteer

或者

> yarn add puppeteer

Puppeteer安装时自带一个最新版本的Chromium，可以通过设置环境变量或者npm config中的PUPPETEER_SKIP_CHROMIUM_DOWNLOAD跳过下载。如果不下载的话，启动时可以通过puppeteer.launch([options])配置项中的executablePath指定Chromium的位置。

#### 使用和例子
```
## 在Node.js文件中加载Puppeteer
const puppeteer = require('puppeteer');
## 使用launch() 方法用于创建浏览器的实例对象:
(async () => {
  const browser = await puppeteer.launch()
})() // 或者promis形式使用 puppeteer.launch().then(async browser => {})
## 调用browser对象的newPage() 方法返回页面对象:

(async () => {
  const browser = await puppeteer.launch()
  const page = await browser.newPage()
})()
## 在页面对象上调用goto() 方法用以访问测试页面:
(async () => {
  const browser = await puppeteer.launch()
  const page = await browser.newPage()
  await page.goto('https://website.com')
})()
## 使用page.$() 等同于使用Selectors API querySelector()获取页面元素, page.$$() 等同于querySelectorAll()获取全部页面满足条件对象。
## 测试结束后可以使用close()方法关闭浏览器:
browser.close()
```
上述代码通过puppeteer的launch方法生成了一个browser的实例，对应于浏览器，launch方法可以传入配置项，比较有用的是在本地调试时传入{headless:false}可以关闭headless模式。

```
const browser = await puppeteer.launch({headless:false})
```

browser.newPage方法可以打开一个新选项卡并返回选项卡的实例page，通过page上的各种方法可以对页面进行常用操作。上述代码就进行了截屏和打印pdf的操作。

一个很强大的方法是page.evaluate(pageFunction, ...args)，可以向页面注入我们的函数，这样就有了无限可能

```
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('http://rennaiqian.com');

  // Get the "viewport" of the page, as reported by the page.
  const dimensions = await page.evaluate(() => {
    return {
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight,
      deviceScaleFactor: window.devicePixelRatio
    };
  });

  console.log('Dimensions:', dimensions);
  await browser.close();
})();
```

需要注意的是evaluate方法中是无法直接使用外部的变量的，需要作为参数传入，想要获得执行的结果也需要return出来。

#### 调试技巧

1.  关掉无界面模式，有时查看浏览器显示的内容是很有用的。使用以下命令可以启动完整版浏览器：

```
const browser = await puppeteer.launch({headless: false})
```

1.  减慢速度，slowMo选项以指定的毫秒减慢Puppeteer的操作。这是另一个看到发生了什么的方法：

```
const browser = await puppeteer.launch({
  headless:false,
  slowMo:250
});
```

3.捕获console的输出,通过监听console事件。在page.evaluate里调试代码时这也很方便：

```
page.on('console', msg => console.log('PAGE LOG:', ...msg.args));
await page.evaluate(() => console.log(`url is ${location.href}`));
```

4.启动详细日志记录，所有公共API调用和内部协议流量都将通过puppeteer命名空间下的debug模块进行记录

```
# Basic verbose logging
 env DEBUG="puppeteer:*" node script.js

 # Debug output can be enabled/disabled by namespace
 env DEBUG="puppeteer:*,-puppeteer:protocol" node script.js # everything BUT protocol messages
 env DEBUG="puppeteer:session" node script.js # protocol session messages (protocol messages to targets)
 env DEBUG="puppeteer:mouse,puppeteer:keyboard" node script.js # only Mouse and Keyboard API calls

 # Protocol traffic can be rather noisy. This example filters out all Network domain messages
 env DEBUG="puppeteer:*" env DEBUG_COLORS=true node script.js 2>&1 | grep -v '"Network'
```

#### 爬虫实践

很多网页通过user-agent来判断设备，可以通过page.emulate(options)来进行模拟。options有两个配置项，一个为userAgent，另一个为viewport可以设置宽度(width)、高度(height)、屏幕缩放(deviceScaleFactor)、是否是移动端(isMobile)、有无touch事件(hasTouch)。

```
const puppeteer = require('puppeteer');
const devices = require('puppeteer/DeviceDescriptors');
const iPhone = devices['iPhone 6'];

puppeteer.launch().then(async browser => {
  const page = await browser.newPage();
  await page.emulate(iPhone);
  await page.goto('https://www.example.com');
  // other actions...
  await browser.close();
});
```

上述代码则模拟了iPhone6访问某网站，其中devices是puppeteer内置的一些常见设备的模拟参数。

很多网页需要登录，有两种解决方案：

1.  让puppeteer去输入账号密码

常用方法：点击可以使用page.click(selector[, options])方法，也可以选择聚焦page.focus(selector)。
输入可以使用page.type(selector, text[, options])输入指定的字符串，还可以在options中设置delay缓慢输入更像真人一些。也可以使用keyboard.down(key[, options])来一个字符一个字符的输入。

1.  如果是通过cookie判断登录状态的可以通过page.setCookie(...cookies)，想要维持cookie可以定时访问。

##### Tip：有些网站需要扫码，但是相同域名的其他网页却有登录，就可以尝试去可以登录的网页登录完利用cookie访问跳过扫码。

#### 简单例子

```
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch({headless: false});
  const page = await browser.newPage();
  await page.goto('https://baidu.com');
  await page.type('#kw', 'puppeteer', {delay: 100});
  page.click('#su')
  await page.waitFor(1000);
  const targetLink = await page.evaluate(() => {
    return [...document.querySelectorAll('.result a')].filter(item => {
      return item.innerText && item.innerText.includes('Puppeteer的入门和实践')
    }).toString()
  });
  await page.goto(targetLink);
  await page.waitFor(1000);
  browser.close();
})()
```
### Page对象内置函数
page.$() 提供对页面上的选择器API方法querySelector()的访问权限

page.$$() 提供对页面上的选择器API方法querySelectorAll()的访问权限

page.$eval() 接收2个或多个参数。第一个是选择器，第二个是函数。如果有更多参数，则这些参数将作为附加参数传递给函数。
```
const innerTextOfButton = await page.$eval('button#submit', el => el.innerText)
```
click()  对页面元素进行点击事件模拟
```
await page.click('button#submit')
```
content() 获取HTML的页面源代码

emulate() 模拟设备信息，可以通过配置Agent和viewport 模拟不同设备访问网站。
```
const puppeteer = require('puppeteer');
const device = require('puppeteer/DeviceDescriptors')['iPhone X'];

puppeteer.launch().then(async browser => {
  const page = await browser.newPage()
  await page.emulate(device)

  //testing

  await browser.close()
})
```
evaluate() 在页上下文中执行用户指定的函数。在这个函数中，可以直接访问document对象，因此可以调用任何DOM API：
```
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch()
  const page = await browser.newPage()
  await page.goto('https://website.com')

  const result = await page.evaluate(() => {
    return document.querySelectorAll('.footer-tags a').length
  })

  console.log(result)
})()
```
ocus() 选中当前元素
goBack() 后退页面
goForward() 前进页面
goto() 打开某一网址
hover() 悬停在某元素上
pdf() 生成当前页面的PDF文档
reload() 重新加载当前页面
screenshot() 截图当前页面并存储为PNG图片
setViewPort() 默认的viewport 为800x600px， 可以通过setViewport 来模拟不同窗口尺寸

waitFor() 等待某些条件成立，内置以下等待条件:
- waitForFunction
- waitForNavigation
- waitForRequest
- waitForResponse
- waitForSelector
- waitForXPath
```
await page.waitFor(waitForNameToBeFilled)
const waitForNameToBeFilled = () => page.$('input#name').value != ''
```