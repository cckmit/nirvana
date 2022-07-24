---
title: iOS-RN-0-45以上版本网络问题无法启动
date: 2021-03-10 21:12:40
categories: react-native
---
RN iOS 0.45以上版本开始需要依赖一些第三方编译库，但这些库在国内下载都非常困难（一般的翻墙工具都很难下载）
未来RN不同版本可能依赖不同版本的第三方编译库，具体所需库和版本请查看[ios-install-third-party.sh](https://github.com/facebook/react-native/blob/master/scripts/ios-install-third-party.sh)文件，注意先把左上角的branch切换到对应的版本.

![1515804427550.jpg](http://upload-images.jianshu.io/upload_images/10024246-4967270af295b740.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在文件最下面找到需要第三方编译库

![1515804585324.jpg](http://upload-images.jianshu.io/upload_images/10024246-182697a23377bd82.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我现在使用的52版本的内容为

```
fetch_and_unpack glog-0.3.4.tar.gz https://github.com/google/glog/archive/v0.3.4.tar.gz 69f91cd5a1de35ead0bc4103ea87294b0206a456 "\"$SCRIPTDIR/ios-configure-glog.sh\""
fetch_and_unpack double-conversion-1.1.5.tar.gz https://github.com/google/double-conversion/archive/v1.1.5.tar.gz 96a8aba1b4ce7d4a7a3c123be26c379c2fed1def
fetch_and_unpack boost_1_63_0.tar.gz https://github.com/react-native-community/boost-for-react-native/releases/download/v1.63.0-0/boost_1_63_0.tar.gz c3f57e1d22a995e608983effbb752b54b6eab741
fetch_and_unpack folly-2016.09.26.00.tar.gz https://github.com/facebook/folly/archive/v2016.09.26.00.tar.gz f3b928b5039235bad6cece638c597c6684d1e4e6
```

下载完后需要把文件copy到***~/.rncache***下，然后就可以运行RN程序了
