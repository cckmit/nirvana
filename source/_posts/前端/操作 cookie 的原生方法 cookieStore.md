---
title: 操作 cookie 的原生方法 cookieStore
date: 2021-12-16 16:28:03
category: javascript
---
现在 Chrome 有了更方便操作 cookie 的方法了`cookieStore`，这个方法是在 Chrome87 版本加入的，兼容性还不太好。

下图是当前日期 2021/03/15 的兼容性概览，可以发现仅仅是 Chrome 体系支持了 cookieStore。

![](https://upload-images.jianshu.io/upload_images/10024246-6c7ab6f44b1d1b59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不过我们可以先来了解它的用法。

> cookieStore 现在只能在`https 协议`下的域名才能访问的到；其他 http 协议的域名里会提示 cookieStore 为 undefined，或者设置失败。

###  基本方法

cookieStore 是一个类似`localStorage`的 object 类型变量。

![](https://upload-images.jianshu.io/upload_images/10024246-99289539d720fb11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到 cookieStore 主要有 5 个方法：

*   set: 设置 cookie，可以是 set(name, value)，也可以是 set({name, value})；
*   get: 获取 cookie，可以是 get(name)，或者 get({name});
*   getAll: 获取所有的 cookie；
*   delete: 删除 cookie；
*   onchange: 监听 cookie 的变化；

前 4 个方法天然支持 Promise。接下来我们一个个来了解下。

### 设置 cookie

cookieStore.set 方法可以设置 cookie，并返回一个 Promise 状态，表示是否设置成功。

```
cookieStore
  .set('username', 'wenzi')
  .then(() => console.log('设置username成功'))
  .catch(() => console.error('设置username失败'));
```

![](https://upload-images.jianshu.io/upload_images/10024246-513eb842ce8d7368.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果我们想要设置更多的属性，例如过期时间，可以传入一个 Object 类型：

```
cookieStore
  .set({
    name: 'age',
    value: 18,
    expires: new Date().getTime() + 24 * 60 * 60 * 1000,
  })
  .then(() => console.log('设置age成功'))
  .catch(() => console.error('设置age失败'));
```

![](https://upload-images.jianshu.io/upload_images/10024246-043bcf0d12305914.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

value 中所有的数据都会默认先执行`toString()`，然后再进行存储，因此有些非基本类型的数据，最好先转换好。

上面都是我们设置 cookie 成功的情况，那么什么时候会设置失败呢？在本地 localhost 环境会设置失败。

本地 localhost，我们是能获取到`cookieStore`这个全局变量，也能执行相应的方法，但无法设置成功：

```cookieStore.set('username', 'wenzi');```

浏览器会发出提示，无法在不安全的域名下通过 CookieStore 中的 set 设置 cookie：

```Uncaught (in promise) TypeError: Failed to execute 'set' on 'CookieStore': Cannot modify a secure cookie on insecure origin```

添加 catch 后，就能捕获到这个错误：

```
cookieStore
  .set('username', 'wenzi')
  .then(() => console.log('设置username成功'))
  .catch(() => console.error('设置username失败'));
```

![](https://upload-images.jianshu.io/upload_images/10024246-6b0e02a480c2df78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因此在想使用 cookieStore 时，不能直接通过下面的方式判断，还得新增一个页面 url 的协议来判断：

```
typeof cookieStore === 'object'; // 判断不准确，本地localhost也会存在
```

应当使用：

```
const isSupportCookieStore = typeof cookieStore === 'object' && location.protocol === 'https:'; // 只有在https协议下才使用cookieStore
```

###  获取 cookie

`cookieStore.get(name)`方法可以获取 name 对应的 cookie，会以 Promise 格式返回所有的属性：

```await cookieStore.get('username');```

![](https://upload-images.jianshu.io/upload_images/10024246-c0cec975d2cff421.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

get()方法还可以接收一个 Object 类型，测试后发现，key 的值只能是 name：

```await cookieStore.get({ name: 'username' });```

当获取的 cookie 不存在时，则返回一个 `Promise<null>`。

### 获取所有的 cookie

`cookieStore.getAll()`方法可以获取当前所有的 cookie，以 Promise<[]>的形式返回的形式返回，数组中的每一项与通过 get()方式获取到的格式一样；若当前域没有 cookie，或者获取不到指定的 cookie，则为空数组；

```await cookieStore.getAll();```

![](https://upload-images.jianshu.io/upload_images/10024246-65cdbbf5dc5009bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

getAll()方法也可以传入一个 name，用来获取对应的 cookie：

```
await cookieStore.getAll('username');
await cookieStore.getAll({ name: 'username' });
```

### 删除 cookie

`cookieStore.delete(name)`用来删除指定的 cookie：

```
cookieStore
  .delete('age')
  .then(() => console.log('删除age成功'))
  .catch(() => console.error('删除age失败'));
```

删除成功后则会提示删除成功。

即使删除一个不存在的 cookie，也会提示删除成功。因此，当再次执行上面的代码时，还是会正常提示。

同样的，在 localhost 环境下会提示删除失败。

###  监听 cookie 的变化

我们可以通过添加`change`事件，来监听 cookie 的变化，无论是通过 cookieStore 操作，还是直接操作 document.cookie，都能监听。

添加监听事件：

```
cookieStore.addEventListener('change', (event) => {
  const type = event.changed.length ? 'change' : 'delete';
  const data = (event.changed.length ? event.changed : event.deleted).map((item) => item.name);

  console.log(`刚才进行了 ${type} 操作，cookie有：${JSON.stringify(data)}`);
});
```

这里面有 2 个重要的字段`changed`数组和`deleted`数组，当设置 cookie 时，则 changed 数组里为刚才设置的 cookie；当删除 cookie 时，则 deleted 数组里为刚才删除的 cookie。

##### 设置操作

当调用 set()方法时，会触发 change 事件，同时影响的 cookie 会放在`event.changed`数组中。

通过 document.cookie 设置或者删除的 cookie，均认为是在修改 cookie，而不是删除。

> 每次设置 cookie 时，即使两次的 name 和 value 完全一样，也会触发`change`事件。

```cookieStore.set('math', 90);```

![](https://upload-images.jianshu.io/upload_images/10024246-66833f29254094d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 删除操作

通过 delete()方法删除一个存在的 cookie 时，会触发 change 事件，被删除的 cookie 会放在`event.deleted`数组中。

如果删除一个不存在的 cookie，则不会触发 change 事件。

```cookieStore.delete('math');```

![](https://upload-images.jianshu.io/upload_images/10024246-f54d1f70d659cc1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，当第二次删除 name 为 math 的 cookie 时，就没有触发 change 事件。

`cookieStore`比我们直接操作 cookie 简便的多，而且还可以通过自身的 change 事件来监听 cookie 的变化。