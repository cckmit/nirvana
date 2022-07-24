---
title: Webpack中hash、chunkhash、contenthash的区别
date: 2021-06-10 16:53:07
categories: webpack
---
在webpack中有时需要使用hash来做静态资源实现增量更新方案之一，文件名的hash值可以有三种hash生成方式，每一种都有不同应用场景.
hash一般是结合CDN缓存来使用，通过webpack构建之后，生成对应文件名自动带上对应的MD5值。如果文件内容发生改变的话，那么对应文件hash值也会改变，对应的HTML引用的URL地址也会改变，触发CDN服务器从原服务器上拉取对应数据，进而更新本地缓存。但是实际使用时，这三种hash计算还是有一定区别。
## hash
hash是根据整个项目构建，只要项目里有文件更改，整个项目构建的hash值都会更改，并且全部文件都共用相同的hash值
```
output: {
    filename: '[name].[hash:8].js',
    path: __dirname + '/built'
},
plugins: [
    // 打包css文件的配置
    new MiniCssExtractPlugin({
    // 这里预设为hash
    filename: 'styles/[name].[hash].css'
  })
]
```
## chunkHash
chunkhash根据不同的入口文件(Entry)进行依赖文件解析、构建对应的代码块（chunk），生成对应的哈希值，某文件变化时只有该文件对应代码块（chunk）的hash会变化
```
output: {
    filename: '[name].[chunkhash:8].js',
    path: __dirname + '/built'
},
plugins: [
    // 打包css文件的配置
    new MiniCssExtractPlugin({
    // 这里预设为hash
    filename: 'styles/[name].[chunkhash].css'
  })
]
```
## contentHash
每一个代码块（chunk）中的js和css输出文件都会独立生成一个hash，当某一个代码块（chunk）中的js源文件被修改时，只有该代码块（chunk）输出的js文件的hash会发生变化
```
output: {
    filename: '[name].[contentHash:8].js',
    path: __dirname + '/built'
},
plugins: [
    // 打包css文件的配置
    new MiniCssExtractPlugin({
    // 这里预设为hash
    filename: 'styles/[name].[contentHash].css'
  })
]
```