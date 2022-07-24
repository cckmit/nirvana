---
title: 灵魂画手：漫画图解 SSH
date: 2021-02-25 21:12:40
categories: git
---
OpenSSL 本身是一个软件库，这个软件被广泛的应用在系统服务器当中，他的主要功能是在网络通信的过程中，保证数据的一致性以及数据传输过程中的安全性。软件本身是由C语言编写，这使得他具备了跨平台的特性，OpenSSL 主要包括如下三大功能:

*   **加解密：** OpenSSL 具有丰富的加解密算法库，支持不同的加解密方式以及存储秘钥的方式，如对称加密，非对称加密，信息摘要等内容
*   **SSL 协议：** OpenSSL 实现了 SSL 协议的的 SSLv2 和 SSLv3，支持了其中绝大部分算法协议
*   **证书操作：** OpenSSL 自身提供了一种文本数据库，支持证书的管理功能，包括证书秘钥的产生、请求产生、证书签发、吊销和验证等功能。

## 加解密的几种形式

加解密的形式通常分为以下几种：

*   对称加密算法
*   非对称加密算法
*   不可逆加密算法
*   下面我们挨个来看这些加密算法。

**对称算法**

对称算法是指信息的发送方和接收方使用同一份秘钥去加解密数据。AES、DES 等都是常用的对称加密算法。

对称算法的优点是加解密的速度快，适合对于大数据量加密。缺点是因为只有一个秘钥，所以秘钥管理困难，只要暴露了，就很容易破解加密后的信息。

![](https://upload-images.jianshu.io/upload_images/10024246-597b16cec5f8a89c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**非对称算法**

非对称算法是指信息的发送方和接收方分别持有一份秘钥。一份公开发布，称公钥；一份私有，称秘钥。秘钥可以导出对应的公钥。RSA、DSA 等是常用的非对称加密算法。

一般情况下，信息发送者用公开密钥去加密，而信息接收者则用私用密钥去解密。公钥机制灵活，但加密和解密速度却比对称密钥加密慢得多。不同的使用场景下，也会衍生出其他的使用方法，比如私钥加密，公钥解密。

![](https://upload-images.jianshu.io/upload_images/10024246-66c99668d3323b92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**RSA 加解密算法**

RSA 是一个流行的非对称加密算法， 生成公私钥内容如下：

```
# 生成秘钥
OpenSSL genrsa -out test.key 1024
# 从秘钥中导出公钥
OpenSSL rsa -in test.key -pubout -out test_pub.key
# 公钥加密文件
echo "test" > hello
OpenSSL rsautl -encrypt -in hello -inkey test_pub.key -pubin -out hello.en
# 私钥解密文件
OpenSSL rsautl -decrypt -in hello.en -inkey test.key -out hello.de
```

**不可逆加密算法**

不可逆加密算法主要用于校验文件的一致性，摘要算法就是其中一种。常用的摘要算法有 MD5。

**摘要算法**

摘要算法用来把任何长度的明文以一定规则变成固定长度的一串字符。在做文件一致性校验的时候，我们通常都是先使用摘要算法，获得固定长度的一串字符，然后对这串字符进行签名。接收者接收到文件后，也会执行一遍摘要算法后再签名。前后数据一致，则表示文件在传输过程没有被窜改。
![](https://upload-images.jianshu.io/upload_images/10024246-faafc26fd81afcae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**base64**

特别需要注意的是，**base64 不是加密算法，它是编码方式。** 它可以方便传输过程 ASCII 码和二进制码之间的转换。类似于图片或者一些文本协议，在传输过程中通常可以使用 base64 转换成二进制码进程传输。

## SSH 加密流程

![](https://upload-images.jianshu.io/upload_images/10024246-efb79de24bee9831.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   客户端发送自己的密钥 ID 给服务器端
*   服务器在自己的 authorized_keys 文件中查找是否存在此 ID 的公钥
*   如果有，则服务器生成一个随机数，用当前 ID 的公钥加密
*   服务器将加密后的随机数发给客户端
*   客户端用私钥解密该随机数，然后在本地为随机数做 MD5 加密
*   客户端将该 MD5 哈希发给服务器端
*   服务器端为一开始自己生成的随机数也做一个 MD5 哈希，然后用通讯通道“公共的密钥”将该哈希加密，再跟客户端发来的内容进行对比。如果双方内容一致，则通过验证，开放访问权限给客户端

深入了解 OpenSSL 后，其对密码学技术的功能支持会让你激动不已，如果有兴趣可以更加深入地了解其中的内容以及试验不同加密方式在不同场景中的使用。放个小预告: 后续还会推出一篇用 pyo3 来给 python 编写 rsa 正反向加解密模块的文章。
