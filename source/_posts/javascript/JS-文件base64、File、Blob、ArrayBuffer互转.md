---
title: JS-文件base64、File、Blob、ArrayBuffer互转
date: 2021-06-10 16:28:03
categories: 前端
---
二进制互转

1. file对象转base64
```
 let reader = new FileReader();
 reader.readAsDataURL(file[0])
 console.log(reader)
```

2. base64 转成blob 上传
```
function dataURItoBlob(dataURI) {  
    var byteString = atob(dataURI.split(',')[1]);  
    var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0];  
    var ab = new ArrayBuffer(byteString.length);  
    var ia = new Uint8Array(ab);  
    for (var i = 0; i < byteString.length; i++) {  
        ia[i] = byteString.charCodeAt(i);  
    }  
    return new Blob([ab], {type: mimeString});  
}
```
3. blob 转成ArrayBuffer
```
let blob = new Blob([1,2,3,4])
let reader = new FileReader();
reader.onload = function(result) {
    console.log(result);
}
reader.readAsArrayBuffer(blob);
```
4. buffer 转成blob
```
let blob = new Blob([buffer])
```
5. base64 转 file
```
const base64ConvertFile = function (urlData, filename) { // 64转file
  if (typeof urlData != 'string') {
    this.$toast("urlData不是字符串")
    return;
  }
  var arr = urlData.split(',')
  var type = arr[0].match(/:(.*?);/)[1]
  var fileExt = type.split('/')[1]
  var bstr = atob(arr[1])
  var n = bstr.length
  var u8arr = new Uint8Array(n)
  while (n--) {
    u8arr[n] = bstr.charCodeAt(n);
  }
  return new File([u8arr], 'filename.' + fileExt, {
    type: type
  });
}
```
