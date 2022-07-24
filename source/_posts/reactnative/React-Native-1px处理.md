---
title: React-Native-1px处理
date: 2021-03-10 21:12:40
categories: react-native
---
当UI设计图上标注某间距为1px时，只需在间距设置的地方填如下值：
```
import {
  View,
  StyleSheet,
  PixelRatio, 
} from 'react-native'
//要用到 PixelRatio.get() 则需从react-native组件中引用 PixelRatio
1px:{
        height:1 / PixelRatio.get()
    }
```
