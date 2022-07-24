---
title: JS获取手机型号和系统版本
date: 2022-06-08 16:28:03
categories: 前端
---
前端浏览器获取设备信息和系统信息只能通过navigator对象的userAgent属性获取。小米9和苹果7plus的userAgent长这样：

[](https://blog.nowcoder.net/n/4ced65ffa81b4acd9994f83593d49d2b?from=nowcoder_improve#)

```
`//米9`

`"Mozilla/5.0 (Linux; Android 9; MI 9 Build/PKQ1.181121.001; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/66.0.3359.126 MQQBrowser/6.2 TBS/044904 Mobile Safari/537.36 MMWEBID/9234 MicroMessenger/7.0.6.1500(0x2700063E) Process/tools NetType/WIFI Language/zh_CN"`

`//苹果7 plus`

`"Mozilla/5.0 (iPhone; CPU iPhone OS 10_2 like Mac OS X) AppleWebKit/602.3.12 (KHTML, like Gecko) Mobile/14C92 Safari/601.1 wechatdevtools/1.02.1907300 MicroMessenger/7.0.4 Language/zh_CN webview/15693784144112003 webdebugger port/58531"`

```

我们现在要获取三个参数

1.  手机型号，这个看上面的只有安卓能获取，这里是MI 9，苹果只能知道是iPhone还是iPad，无法知道具体手机型号。
2.  系统版本，安卓苹果手机都能获取，上面的安卓是Android 9，苹果是iPhone OS 10_2。
3.  微信版本，如果在微信浏览器中打开，我们需要获取微信的版本。这个苹果安卓都能获取，上面显示的是MicroMessenger/7.0.4

先判断是苹果还是安卓，代码如下：

```
let userAgent = navigator.userAgent
if (userAgent.includes('iPhone') || userAgent.includes('iPad')) {
  console.log('苹果手机')
}
if (userAgent.includes('Android')) {
  console.log('安卓手机')
}

```

下面我们用正则分别来获取这三个需要的参数

**系统版本（安卓）：**

这里的 (?=;) 是指到分号为止，但不包括分号。第一个问号，就是 /Android.*?(?=;)/ 是指匹配第一个后面括号里的内容，也就是匹配第一个分号。

```

let m1 = userAgent.match(/Android.*?(?=;)/)
if (m1 && m1.length > 0) {
  system = m1[0]    // Android 9
}

```
**系统版本（苹果）：**

这个和上面一样，只是匹配空格。

```
let m1 = userAgent.match(/iPhone OS .*?(?= )/)
if (m1 && m1.length > 0) {
  system = m1[0]    // iPhone OS 10_2
}

```

**微信版本：**
```
let m1 = userAgent.match(/MicroMessenger.*?(?= )/)
if (m1 && m1.length > 0) {
  wechat = m1[0]    // MicroMessenger/7.0.4
}
```

**手机型号（安卓）：**

这里由于包含一个非问号开头的括号表示，所以match会有2个结果，第一个是全部匹配，第二个是括号内的匹配，我们只要括号内的匹配。

```

let m1 = userAgent.match(/Android.*; ?(.*(?= Build))/)
if (m1 && m1.length > 1) {
  device = m1[1]    // MI 9
}

```

# 完整代码

```

let webLog = {}
let userAgent = navigator.userAgent
// 获取微信版本
let m1 = userAgent.match(/MicroMessenger.*?(?= )/)
if (m1 && m1.length > 0) {
  webLog.wechat = m1[0]
}
// 苹果手机
if (userAgent.includes('iPhone') || userAgent.includes('iPad')) {
  // 获取设备名
  if (userAgent.includes('iPad')) {
    webLog.device = 'iPad'
  } else {
    webLog.device = 'iPhone'
  }
  // 获取操作系统版本
  m1 = userAgent.match(/iPhone OS .*?(?= )/)
  if (m1 && m1.length > 0) {
    webLog.system = m1[0]
  }
}
// 安卓手机
if (userAgent.includes('Android')) {
  // 获取设备名
  m1 = userAgent.match(/Android.*; ?(.*(?= Build))/)
  if (m1 && m1.length > 1) {
    webLog.device = m1[1]
  }
  // 获取操作系统版本
  m1 = userAgent.match(/Android.*?(?=;)/)
  if (m1 && m1.length > 0) {
    webLog.system = m1[0]
  }
}

```