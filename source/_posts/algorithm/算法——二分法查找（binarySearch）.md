---
title: 算法——二分法查找（binarySearch）
date: 2021-03-10 21:12:40
categories: 算法学习
---
二分法查找，也称为折半法，是一种在有序数组中查找特定元素的搜索算法。

二分法查找的思路如下：
（1）首先，从数组的中间元素开始搜索，如果该元素正好是目标元素，则搜索过程结束，否则执行下一步。
（2）如果目标元素大于/小于中间元素，则在数组大于/小于中间元素的那一半区域查找，然后重复步骤（1）的操作。
（3）如果某一步数组为空，则表示找不到目标元素。
这种搜索算法每一次比较都使搜索范围缩小一半。时间复杂度`O(logn)`。
##### 非递归写法
```
/**
 * 二分法查找
 * @param {array[T]} arr 查询数组
 * @param {T} key 查找对象
 */
function binarySearch(arr, key) {
  var start = 0; //数组最小索引值
  var end = arr.length - 1; //数组最大索引值
  while (start <= end) {
    var mid = Math.floor((start + end) / 2);
    if (key == arr[mid]) {
      return mid;
    } else if (key > arr[mid]) {
      start = mid + 1;
    } else {
      end = mid - 1;
    }
  }
  return null; //start>end的情况，这种情况下key的值大于arr中最大的元素值或者key的值小于arr中最小的元素值
}
```
##### 递归写法
```
/**
 * 递归方式二分法查询
 * @param {array[T]} arr 查询数组
 * @param {number} start 开始位置
 * @param {number} end 结束位置
 * @param {T} key 查找对象
 */
function binarySearch(arr, start, end, key) {
  if (start > end) {
    return null;
  }
  var mid = Math.floor((start + end) / 2);
  if (key == arr[mid]) {
    return mid;
  } else if (key < arr[mid]) {
    end = mid - 1;
    return binarySearch(arr, start, end, key);
  } else {
    start = mid + 1;
    return binarySearch(arr, start, end, key);
  }
}
```
