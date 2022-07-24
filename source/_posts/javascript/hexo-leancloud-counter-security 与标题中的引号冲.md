---
title: hexo-leancloud-counter-security 与标题中的引号冲突
date: 2021-12-09 16:28:03
categories: 前端
---
配置博客阅读次数时，遇到 `hexo-leancloud-counter-security` 插件的一个冲突。

完成配置使用 `hexo -d` 时，终端中出现下面的错误提示：

```
FATAL {
  err: SyntaxError: Unexpected token u in JSON at position 28
      at JSON.parse (<anonymous>)
      at /Users/liuzhiwei022/my/nirvana/node_modules/hexo-leancloud-counter-security/index.js:128:22
      at Array.forEach (<anonymous>)
      at Hexo.sync (/Users/liuzhiwei022/my/nirvana/node_modules/hexo-leancloud-counter-security/index.js:121:20)
      at processTicksAndRejections (internal/process/task_queues.js:93:5)
}
 Something's wrong. Maybe you can find the solution here: %s https://hexo.io/docs/troubleshooting.html
```
祭出输出调试大法，增加打印：
```
while (memoData[memoIdx] !== ']') {
            //增加打印
            console.log(memoData[memoIdx].substring(0, memoData[memoIdx].length - 1))
            y = JSON.parse(memoData[memoIdx].substring(0, memoData[memoIdx].length - 1));
            if (y.url > x.url) break;
            if (y.url === x.url && y.title === x.title) {
                flag = true;
                break;
            }
            memoIdx++;
        }
```

然后再执行 `hexo -d` 命令，命令行到下面开始报错：

```
{"title":"nginx:[warn] the "user" directive makes sense only if the master process runs with super-user","url":"/javascript/前端/nginx- [warn] the "user" directive makes sense only if the master process runs with super-user/"}
```
JSON 在解析字符串时出现错误。对应的正是之前写的一篇名为 `nginx:[warn] the "user" directive makes sense only if the master process runs with super-user` 的文章，由于 JSON 格式中字符串是需要用`""` 修饰，导致JSON 中出现了一个 `"title":"nginx:[warn] the "user" directive makes sense only if the master process runs with super-user"` key-value 组合。这个时候JSON解析报错。
定位问题之后，文章的标题绕过部署失败。
