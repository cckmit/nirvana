---
title: 快照React Native视图并将其保存到图像中
date: 2022-05-20 21:12:40
categories: 
    - [react-native]
---
# react-native-view-shot 
将React Native视图捕获到图像中。

## Install

```
yarn add react-native-view-shot

# In Expo

expo install react-native-view-shot

```

确保`react-native-view-shot`在Xcode中正确链接（可能需要手动安装，请参阅React Nativedoc）。

0.60.x之前你必须：

```
react-native link react-native-view-shot

```

由于0.60.x，autolink应该可以正常工作，在iOS上，您需要确保CocoaPods安装有：

```
npx pod-install

```

## Recommended High Level API

```
import ViewShot from "react-native-view-shot";

class ExampleCaptureOnMountManually extends Component {
  componentDidMount () {
    this.refs.viewShot.capture().then(uri => {
      console.log("do something with ", uri);
    });
  }
  render() {
    return (
      <ViewShot ref="viewShot" options={{ format: "jpg", quality: 0.9 }}>
        <Text>...Something to rasterize...</Text>
      </ViewShot>
    );
  }
}

// alternative
class ExampleCaptureOnMountSimpler extends Component {
  onCapture = uri => {
    console.log("do something with ", uri);
  }
  render() {
    return (
      <ViewShot onCapture={this.onCapture} captureMode="mount">
        <Text>...Something to rasterize...</Text>
      </ViewShot>
    );
  }
}

// waiting an image
class ExampleWaitingCapture extends Component {
  onImageLoad = () => {
    this.refs.viewShot.capture().then(uri => {
      console.log("do something with ", uri);
    })
  };
  render() {
    return (
      <ViewShot ref="viewShot">
        <Text>...Something to rasterize...</Text>
        <Image ... onLoad={this.onImageLoad} />
      </ViewShot>
    );
  }
}

// capture ScrollView content
class ExampleCaptureScrollViewContent extends Component {
  onCapture = uri => {
    console.log("do something with ", uri);
  }
  render() {
    return (
      <ScrollView>
        <ViewShot onCapture={this.onCapture} captureMode="mount">
          <Text>...The Scroll View Content Goes Here...</Text>
        </ViewShot>
      </ScrollView>
    );
  }
}

```

**Props:**

*   `children`：要光栅化的实际内容。
*   `options`：与`captureRef`方法中的选项相同。
*   `captureMode`（字符串）：如果未定义（默认）。捕获不是自动的，您需要使用ref并自己调用`capture()`。`"mount"`。在装载时捕获一次视图。（重要的是要了解图像加载是不会等待的，在这种情况下，您要使用`"none"`和`viewShotRef.capture()`在`Image#onLoad`之后。）`"continuous"`实验性的，这将连续捕获大量图像。对于非常具体的use-cases。`"update"`实验性的，每次React重画（在did更新时）都会捕获图像。对于非常具体的use-cases。
*   `onCapture`：当一个`captureMode`被定义时，这个回调函数将被调用并得到捕获结果。
*   `onCaptureFailure`：定义`captureMode`时，当捕获失败时将调用此回调。


## `captureRef(view, options)`低级命令式API

```
import { captureRef } from "react-native-view-shot";

captureRef(viewRef, {
  format: "jpg",
  quality: 0.8
}).then(
  uri => console.log("Image saved to", uri),
  error => console.error("Oops, snapshot failed", error)
);

```

返回图像URI的承诺。

*   `view`是对React Native组件的引用。
*   `options`可能包括：`width`/`height`（数字）：最终图像的宽度和高度（根据视图范围调整大小）。如果您不想提供原始像素大小，请不要提供）。`format`（字符串）`png`或`jpg`或`webm`（Android）。默认为`png`。`quality`（数字）：质量。0.0-1.0（默认）。（仅适用于jpg之类的有损格式`result`（string），您希望用来保存快照的方法之一：`"tmpfile"`（默认值）：保存到临时文件（只有在应用程序运行时才会存在）。`"base64"`：编码为base64并返回原始字符串。仅用于小图像，因为这可能是滞后的结果（字符串通过网桥发送）。N、 这不是一个数据uri，请使用`data-uri`。`"data-uri"`：与`base64`相同，但也包括dataurischeme头。`snapshotContentContainer`（bool）：如果为true，并且当视图为滚动视图时，将计算“内容容器”高度，而不是容器高度。

## `releaseCapture(uri)`

此方法将释放先前捕获的`uri`。对于tmpfile，它将清除它们，对于其他结果类型，它什么也不做。

注意：tmpfile捕获在应用程序关闭后会自动清除，因此您可能不必担心这一点，除非有高级用例。`ViewShot`组件将在每次多次捕获时使用它（对于连续捕获以避免文件泄漏非常有用）。

## `captureScreen()`仅限Android和iOS

```
import { captureScreen } from "react-native-view-shot";

captureScreen({
  format: "jpg",
  quality: 0.8
}).then(
  uri => console.log("Image saved to", uri),
  error => console.error("Oops, snapshot failed", error)
);

```

此方法将捕获当前显示屏幕的内容作为本机硬件屏幕截图。它不需要ref输入，因为它不在视图级别工作。这意味着滚动视图不会被完整地捕获，只捕获用户当前可见的部分。

返回图像URI的承诺。

*   `options`：与`captureRef`方法中的选项相同。

### Advanced Examples

[Checkout react-native-view-shot-example](javascript:void(0);)

## Interoperability Table

> 快照不能保证像素完美。这也取决于平台。以下是我们注意到的一些差异以及如何解决问题。

测试机型：iPhone6（iOS）、Nexus5（Android）。

| System | iOS | Android | Windows |
| --- | --- | --- | --- |
| View,Text,Image,.. | YES | YES | YES |
| WebView | YES | YES<sup>1</sup> | YES |
| gl-react v2 | YES | NO<sup>2</sup> | NO<sup>3</sup> |
| react-native-video | NO | NO | NO |
| react-native-maps | YES | NO<sup>4</sup> | NO<sup>3</sup> |
| react-native-svg | YES | YES | maybe? |
| react-native-camera   | NO               | YES               | NO <sup>3</sup>        |

1.  仅通过包装`<View collapsable={false}>`父对象并对其进行快照支持。
2.  它返回一个空图像（不是失败的承诺）。
3.  组件本身缺乏平台支持。
4.  但是您可以使用react-native-maps快照函数：https://github.com/airbnb/react-native-maps拍摄地图快照

## Performance Optimization

在分析过程中，捕捉到了影响性能的几个因素：

1.  (de-)allocation的位图内存
2.  Base64输出缓冲区的(de-)allocation内存
3.  将位图压缩为不同的图像格式：PNG、JPG

为了在代码中解决这个问题，引入了几种新方法：

*   可重用的图像，减少GC的负载；
*   可重用的数组/缓冲区也可以减少GC的负载；
*   原始图像格式，避免昂贵的压缩；
*   压缩压缩原始数据，这比`Bitmap.compress`更快

更多细节和代码片段如下。

### RAW Images

介绍了一种新的图像格式RAW。它对应一个ARGB像素数组。

Advantages:

*   没有压缩，所以晚餐很快。截图小于16ms；

`zip-base64`、`base64`和`tmpfile`结果类型支持的原始格式。

磁盘上的原始文件以`${width}:${height}|${base64}`字符串的格式保存。

### zip-base64

与BASE64结果字符串相比，这种格式快速尝试对屏幕截图结果应用zip/deflate压缩，并且仅在压缩后将结果转换为BASE64字符串。结合使用zip-base64+raw，我们获得了一种捕获屏幕视图并将其交付到react端的超快速方法。

### 如何使用zip-base64和原始格式？

```
const fs = require("fs");
const zlib = require("zlib");
const PNG = require("pngjs").PNG;
const Buffer = require("buffer").Buffer;

const format = Platform.OS === "android" ? "raw" : "png";
const result = Platform.OS === "android" ? "zip-base64" : "base64";

captureRef(this.ref, { result, format }).then(data => {
  // expected pattern 'width:height|', example: '1080:1731|'
  const resolution = /^(\d+):(\d+)\|/g.exec(data);
  const width = (resolution || ["", 0, 0])[1];
  const height = (resolution || ["", 0, 0])[2];
  const base64 = data.substr((resolution || [""])[0].length || 0);

  // convert from base64 to Buffer
  const buffer = Buffer.from(base64, "base64");
  // un-compress data
  const inflated = zlib.inflateSync(buffer);
  // compose PNG
  const png = new PNG({ width, height });
  png.data = inflated;
  const pngData = PNG.sync.write(png);
  // save composed PNG
  fs.writeFileSync(output, pngData);
});

```

请记住，将PNG数据打包为`zlib.inflate`是一个占用CPU的操作。

提示：使用`process.fork()`方法将原始数据转换为png。

> 注：代码是在大型商业项目中测试的。

> 注意#2：别忘了在项目中添加包：
> 
> ```
> yarn add pngjs
> yarn add zlib
> 
> ```

## 故障排除/常见问题解答

### 保存到文件？

*   如果要将快照图像结果保存到CameraRoll，只需使用https://facebook.github.io/react-native/docs/cameraroll.html#savetocameraroll
*   如果要将其保存到任意文件路径，请使用https://github.com/itinance/react-native-fs
*   对于任何更高级的需求，您可以编写自己的（或找到另一个）本机模块来解决您的use-case。

### 快照因错误而被拒绝？

*   对特殊组件（如视频/GL视图）的支持不能保证正常工作。如果失败，`captureRef`承诺将被拒绝（库不会崩溃）。

### 得到一个黑色或空白的结果，还是仍然有一个错误的简单视图？

检查上面的互操作性表。遗憾的是，某些特殊组件不受支持。如果您的视图包含不受支持的组件之一，则整个快照也可能受到损害。

### 文本周围出现黑色背景而不是透明/奇怪的边框？

*   最好在栅格化的视图上使用背景色，以避免透明像素和文本周围出现某些边框的潜在怪异。

### 在Android上，获取“试图解析带有不存在的标记{tagID}'的视图”

> 如果您想要快照视图，您需要确保`collapsable`设置为`false`。有些内容甚至需要包装成这样的`<View collapsable={false}>`才能真正使它们成为快照！否则，该视图不会反映任何UI视图。（由@gaguirre发现）

或者，您可以使用`ViewShot`组件，它将设置`collapsable={false}`来解决这个问题。

### 获取“内容大小不能为零或negative.”

> 请确保不要立即进行快照，至少需要等待第一个`onLayout`事件，或者在超时之后，否则视图可能还没有准备好。（如果您有图像的话，可以安全地等待图像`onLoad`）。如果仍有问题，请确保视图的宽度和高度实际大于0。

或者，您可以使用`ViewShot`组件来等待第一个`onLayout`。

### 快照图像与我的宽度和高度不匹配，但twice/3-times大

这是因为快照图像的结果是实际像素大小的，其中React Native样式中定义的宽度/高度是以“点”单位定义的。您可能需要设置宽度和高度选项来强制调整大小。（可能会影响图像质量）
