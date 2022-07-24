---
title: 字体之TTF、TOF、WOFF 和 WOFF2
date: 2022-02-02 16:28:03
categories: 前端
---
## TTF（TrueType Font）
---
`TrueType `是由美国苹果公司和微软公司共同开发的一种电脑轮廓字体（曲线描边字）类型标准。
这种类型字体文件的扩展名是 `.ttf`，类型代码是` tfil`。
`TrueType`的主要强项在于它能给开发者提供关于字体显示、不同字体大小的像素级显示等的高级控制。

## OTF（OpenType Font）
---
`OpenType` 是 Adobe 和 Microsoft 联合开发的跨平台字体文件格式，也叫 Type 2 字体，它的字体格式采用 Unicode 编码，是一种兼容各种语言的字体格式。
`OpenType` 也是一种轮廓字体，比`TrueType` 更为强大，并且还支持多个平台，支持很大的字符集，还有版权保护。可以说它是Type 1和 TrueType 的超集。
`OpenType `标准定义了 `OpenType` 文件名称的后缀名：

- 包含 TrueType 字体的 OpenType 文件后缀名为 `.ttf`。
- 包含 PostScript 字体的文件后缀名为 `.OTF`。
- 如果是包含一系列 TrueType 字体的字体包文件，那么后缀名为` .TTC`。

OTF 的主要优点有：

- 增强的跨平台功能；
- 更好的支持Unicode标准定义的国际字符集；
- 支持高级印刷控制能力；
- 生成的文件尺寸更小；
- 支持在字符集中加入数字签名，保证文件的集成功能。

同一个 OpenType 字体文件可以用于 Mac OS，Windows 和 Linux 系统，这种跨平台的字库非常方便于用户的使用，用户再也不必为不同的系统配制字库而烦恼了。
OTF 的兼容性和 TTF 相同。

## WOFF（Web Open Font Format）
---
`Web 开放字体格式`是一种网页所采用的字体格式标准。此字体格式发展于 2009 年，现在正由万维网联盟的 Web 字体工作小组标准化，以求成为推荐标准。
此字体格式不但能够有效利用压缩来减少档案大小，并且不包含加密也不受 DRM（数位著作权管理）限制。
WOFF 本质上是包含了基于 sfnt 的字体（如 `TrueType`、`OpenType` 或开放字体格式），且这些字体均经过 WOFF 的编码工具压缩，以便嵌入网页中。这个字体格式使用zlib压缩，文件大小一般比 TTF 小` 40%`。
## WOFF2
---
WOFF 2 标准在 WOFF1 的基础上，进一步优化了体积压缩，带宽需求更少，同时可以在移动设备上快速解压。
与 WOFF 1.0 中使用的 Flate 压缩相比，WOFF 2.0 是使用 Brotli 方法进行的压缩，压缩率更高，所以文件体积更小。

---
- `TTF `的兼容性更好，但是其字体文件体积最大。
- `WOFF` 字体比 TTF 字体有更小的体积和更好的表现性。
- `WOFF 2` 字体是对 `WOFF` 字体的升级。
