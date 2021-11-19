---
title: 检查浏览器是否支持webp
date: 2021-10-31 16:13:30
category: javascript
---

部分浏览器支持`webp`，部分浏览器不支持`webp`，下面用代码来检查下浏览器是否支持webp，方便决定让浏览器加载服务器的webp文件，还是jpg文件

[![image](https://upload-images.jianshu.io/upload_images/10024246-48c235ca822015c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://java-er.com/blog/check-webp-code/webp/) 

1\. MAC系统 Chrome Version 79.0.3945.130 (Official Build) (64-bit) 返回true
2\. MAC系统 Safari 13.0.5 返回 false
3\. MAC Firefox 60.9.0esr (64 位) 返回false
4\. Windows Firefox 56.0.1 (64位) 返回false
5\. Windows Firefox 73.0.1 (64位) 返回true

**方法1：Google官方写法**
这里提供了几种webp的图片模式，如果浏览器支持webp，那么图片的宽高会大于0，从而返回true,否则返回false.

第一个参数feature可以传 lossy,lossless,alpha,animation中的一个，第一个传个回调函数。获取他result。如果支持，返回ture,否则返回false。可以再谷歌和IE下试试，谷歌返回ture，IE返回false

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>测试WEBP支持</title>
</head>
<body>
    <div id="msg"></div>
</body>
<script>
function check_webp_feature(feature, callback) {
    var kTestImages = {
        lossy: "UklGRiIAAABXRUJQVlA4IBYAAAAwAQCdASoBAAEADsD+JaQAA3AAAAAA",
        lossless: "UklGRhoAAABXRUJQVlA4TA0AAAAvAAAAEAcQERGIiP4HAA==",
        alpha: "UklGRkoAAABXRUJQVlA4WAoAAAAQAAAAAAAAAAAAQUxQSAwAAAARBxAR/Q9ERP8DAABWUDggGAAAABQBAJ0BKgEAAQAAAP4AAA3AAP7mtQAAAA==",
        animation: "UklGRlIAAABXRUJQVlA4WAoAAAASAAAAAAAAAAAAQU5JTQYAAAD/////AABBTk1GJgAAAAAAAAAAAAAAAAAAAGQAAABWUDhMDQAAAC8AAAAQBxAREYiI/gcA"
    };
    var img = new Image();
    img.onload = function () {
        var result = (img.width > 0) && (img.height > 0);
        callback(feature, result);
    };
    img.onerror = function () {
        callback(feature, false);
    };
    img.src = "data:image/webp;base64," + kTestImages[feature];
}

check_webp_feature('lossless',function(feature,result){
   document.getElementById("msg").innerHTML = "isSupportWebp: " + result;
 });

</script>
</html>
```

**方法2: 生成一个图片来检查**

```
<body>
    <div id="msg"></div>
</body>

<script>
//备注，JS 代码比如放在<div id="msg"></div> 下方，
//如果放在上方在 Safari 下报错，在chrome下不报错

const supportsWebp = ({ createImageBitmap, Image }) => {
  if (!createImageBitmap || !Image) return Promise.resolve(false);

  return new Promise(resolve => {
      const image = new Image();
      image.onload = () => {
          createImageBitmap(image)
              .then(() => {
                  resolve(true);
              })
              .catch(() => {
                  resolve(false);
              });
      };
      image.onerror = () => {
          resolve(false);
      };
      image.src = 'data:image/webp;base64,UklGRh4AAABXRUJQVlA4TBEAAAAvAAAAAAfQ//73v/+BiOh/AAA=';
  });
};

const webpIsSupported = () => {
  let memo = null;
  return () => {
      if (!memo) {
          memo = supportsWebp(window);
      }
      return memo;
  };
};

webpIsSupported()().then(res => {
    //console.log("是否支持 webp", res)
    document.getElementById("msg").innerHTML = "是否支持 webp: " + res;
}).catch(err => {
    //console.log(err)
    document.getElementById("msg").innerHTML = "err: " + err;
})

</script>
```

**方法3: 加载canvas的方法来检查，比较简洁**

```
<body>
    <div id="msg"></div>
</body>

<script>
var isSupportWebp = function () {
  try {
    return document.createElement('canvas').toDataURL('image/webp', 0.5).indexOf('data:image/webp') === 0;
  } catch(err) {
    return false;
  }
}

document.getElementById("msg").innerHTML = "isSupportWebp: " + isSupportWebp();

</script>
```

**方法4: 利用浏览器的支持情况，后端检查**
![](https://upload-images.jianshu.io/upload_images/10024246-b1aa1599fed193eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

利用服务端程序判断HTTP_ACCEPT这个值 php代码为例子

```
<?php
$str = $_SERVER['HTTP_ACCEPT'];
$result = 'NO';
if(strstr($str, 'image/webp')!=false){
    $result = "YES";
}

echo $str;
echo "<br />is support webp : ".$result;
?>
```

支持的chrome返回

```
text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
is support webp : YES
```

不支持的Firefox 返回

```
text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
is support webp : NO
```
