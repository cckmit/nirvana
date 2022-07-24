---
title: Web页面图片资源优化策略
date: 2022-05-06 16:28:03
categories: 前端
---
## 1.tinify 压缩
![](https://upload-images.jianshu.io/upload_images/10024246-073c6caf646cb338.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里我用了 [tinify.cn](https://tinify.cn/) 这个网站，个人认为它的质量最高，而且支持申请[开发者 API](https://tinify.cn/developers)，每个月有 500 张的免费份额，算了一下 COS 上一共 450 张图片，正好在额度内，没什么犹豫的直接开压。

最后的压缩成果如下：

| 压缩前 | 压缩后 |
| --- | --- |
| 111MB | 75MB |

综合看上去只缩减了 **33%** 的体积，实际上压缩比要比这个高，大概能省 **60%** 左右。
## 2.gzip 压缩
gzip压缩是基于deflate中的算法进行压缩的，gzip会产生自己的数据格式，gzip压缩对于所需要压缩的文件，首先使用LZ77算法进行压缩，再对得到的结果进行huffman编码，根据实际情况判断是要用动态huffman编码还是静态huffman编码，最后生成相应的gz压缩文件。
##### 使用 gzip 方式进行压缩
客户端可以事先声明一系列的可以支持压缩模式，与请求一齐发送。 `Accept-Encoding`这个首部就是用来进行这种内容编码形式协商的：
```
Accept-Encoding: gzip, deflate
```
服务器在 Content-Encoding 响应首部提供了实际采用的压缩模式：
```
Content-Encoding: gzip
```

##### nginx配置压缩

*   开启gzip

```
gzip on;

```

*   启用gzip压缩的最小文件，小于设置值的文件将不会压缩

```
gzip_min_length 1k;

```

*   gzip 压缩级别，1-10，数字越大压缩的越好，也越占用CPU时间

```
gzip_comp_level 2;

```

*   进行压缩的文件类型。其中的值可以在 [mime.types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)找到

```
gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript;
```
##### web服务器处理http压缩的过程
    1. Web服务器接收到浏览器的HTTP请求后，检查浏览器是否支持HTTP压缩（Accept-Encoding 信息）；

    2. 如果浏览器支持HTTP压缩，Web服务器检查请求文件的后缀名；

    3. 如果请求文件是HTML、CSS等静态文件，Web服务器到压缩缓冲目录中检查是否已经存在请求文件的最新压缩文件；

    4. 如果请求文件的压缩文件不存在，Web服务器向浏览器返回未压缩的请求文件，并在压缩缓冲目录中存放请求文件的压缩文件；

    5. 如果请求文件的最新压缩文件已经存在，则直接返回请求文件的压缩文件；

    6. 如果请求文件是动态文件，Web服务器动态压缩内容并返回浏览器，压缩内容不存放到压缩缓存目录中。

下面是两个演示图：

未使用Gzip：

![](https://upload-images.jianshu.io/upload_images/10024246-8c3381658b3ac948.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

开启使用Gzip后：
![](https://upload-images.jianshu.io/upload_images/10024246-0347939a0623b180.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4.webp 在线转换
[WebP](https://developers.google.com/speed/webp/)，是一种支持有损压缩和无损压缩的图片文件格式，派生自图像编码格式 VP8。根据 Google 的测试，无损压缩后的 WebP 比 PNG 文件少了 45％ 的文件大小，即使这些 PNG 文件经过其他压缩工具压缩之后，WebP 还是可以减少 28％ 的文件大小。
WebP的优点
- 压缩率高
- 显示效果好
- 占用空间小
- 加载速度快
现在主流浏览器基本都支持该格式，同时一些图床网站还支持智能转换webp的服务
## CDN 加速
CDN 服务开启后，可以利用CDN的缓存功能，有效降低资源请求，同时加载速度直接提升一个数量级。之前的图片如果是从 源站请求的，每张图片响应速度大概为 200ms-300ms，开启 CDN 后直接降到 20ms-30ms，劣化情况下也能保持在 100ms 内：
![](https://upload-images.jianshu.io/upload_images/10024246-b166506f3cfbfdc5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

