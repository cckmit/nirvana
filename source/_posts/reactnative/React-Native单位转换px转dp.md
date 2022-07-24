---
title: React-Native单位转换px转dp
date: 2022-05-04 21:12:40
categories: 
    - [react-native]
tags: [react-native]
---

在开发 `React-Native` 项目的时候通常会涉及到单位转换的问题，这里整理了常用的写法。

## 计算公式
手机中元素的宽度 = 手机屏幕宽度 * 元素宽度 / 设计稿宽度

## 工具类 utils.js 
```
/*
* @desc 屏幕尺寸适配
* @params {String} uiElementPx 1倍稿元素尺寸
* @params {Number} uiWidth     设计稿稿尺寸(default 375)
* @return {number}
*/
import { Dimensions, PixelRatio, Platform } from "react-native";

const pxToDp = (uiElementPx: number, isInt = true, uiWidth = 375): number | string => {
        const deviceWidthDp = Dimensions.get("window").width;//屏幕宽
	if (typeof uiElementPx === "string") {
		uiElementPx = Number(uiElementPx);
	}
	if (uiElementPx <= 3) {
		return uiElementPx;
	}
	if (Platform.OS === "web") {
		return (uiElementPx * 10 / uiWidth) + "rem";
	} else {
		const mathResult = uiElementPx * deviceWidthDp / uiWidth;
		const resultInt = isInt ? Math.ceil(mathResult) : mathResult;
		return mathResult > (1 / PixelRatio.get()) ? resultInt : 1 / PixelRatio.get();
	}
};
```