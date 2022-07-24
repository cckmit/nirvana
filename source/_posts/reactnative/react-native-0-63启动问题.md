---
title: react-native-0-63启动问题
date: 2021-03-10 21:12:40
categories: react-native
---
1、`OpenSSL-Universal` requires CocoaPods version `>= "1.9`"
podinstall时提示需要把CocoaPods升级到1.9，但是通过brew install CocoaPods，发现安装的版本是1.6.1，brew upgrade CocoaPods 发现版本无法升级，后来查到1.9的版本需要通过`gem install -n /usr/local/bin cocoapods --pre`才能安装最新的版本
2、Typedef redefinition with different types ('uint8_t' (aka 'unsigned char') vs 'enum clockid_t')
changing `iOS Deployment Target` of `Flipper-Folly Pods` to "iOS 9.0", this error disappeared.
[![](https://upload-images.jianshu.io/upload_images/10024246-e337b36acb480adc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://user-images.githubusercontent.com/3139234/78309578-52984600-7586-11ea-9d27-cefdc6e7ca0f.png)
3、 'event2/event-config.h' file not found
 Flipper-Folly的版本有问题，需要指定版本
use_flipper!修改为use_flipper!({ 'Flipper-Folly' => '2.3.0' })
重新pod install即可
