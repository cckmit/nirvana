---
title: Web components
date: 2021-12-03 16:13:30
category: javascript
---
## WC 到底是什么？

援引MDN上的解释：

> Web Components consists of several separate technologies. You can think of Web Components as reusable user interface widgets that are created using open Web technology. They are part of the browser and so they do not need external libraries like jQuery or Dojo. An existing Web Component can be used without writing code, simply by adding an import statement to an HTML page. Web Components use new or still-developing standard browser capabilities.

简单的讲，Web Component 就是把组件封装成 html 标签的形式，并且在使用时不需要写额外的 js 代码。

**组件是前端的发展方向，抛开周边技术生态，单纯看 React 和 Vue 都是组件框架**。因此，WC 可以视为原生标签的拓展/延伸，说到底，它依旧是一个标签！

类似 `<video></video>` 标签，相比于原生标签，它多了更为丰富的样式和可操作属性。
![](https://upload-images.jianshu.io/upload_images/10024246-15af1ab1acf739f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


谷歌公司由于掌握了 Chrome 浏览器，一直在推动浏览器的原生组件，即 Web Components API。相比第三方框架，原生组件简单直接，符合直觉，不用加载任何外部模块，代码量小。貌似一切完美，似乎大有可以用来替换React、Vue之类的趋势。

很多人也提出过将它与三大前端框架比较，比如[《Web Component 和类 React、Angular、Vue 组件化技术谁会成为未来？》](https://www.zhihu.com/question/58731753/answer/829838462)，其实它们是两个领域的东西，不具有可比性。WC 最大的优势在于 CSS 防污染，浏览器原生支持的组件化实现；而 Vue 等 MVVM 框架注重数据分离和自动化绑定的实现。且 WC 中包含的 4 大 web api 是标准规范，使用上滞后性（比如新落地的规范往往要等几年后才会被人拿出来使用），但 vue、react、ng 等框架不会。

## 目前存在的缺陷

与其它 web 框架一起使用存在一些小问题，会给开发体验上造成一些困扰。

## 1、组件内部事件的回调

比如，一个弹窗组件（`<my-dialog></my-dialog>`）中的确定按钮，那么它的事件是如何触发的呢？

```
class myDialog extends HTMLElement {
  // ...
  connectedCallback() {
    const shadowRoot = this.attachShadow({ mode: 'open' });
    shadowRoot.innerHTML = `
      <div class="dialog">
        <div class="dialog-content">
          <div class="dialog-body">
            弹窗内容
          </div>

          <button id="okBtn">确定</button>
        </div>
      </div>
    `;

    shadowRoot.querySelector('#okBtn').addEventListener('click', () => {
      // 组件内部定义事件
      this.dispatchEvent(new CustomEvent('okBtnFn'));
    });
  }
}

customElements.define('my-dialog', myDialog);
```

现在方案是 custom element 内部自定义事件 `new CustomEvent()`，外部用 `addEventListener`监听。这样的写法是很丑陋的，仿佛又回到了原生 JS 写应用的时代。

```
<my-dialog></my-dialog>

<script> export default {
    created() {
      document.addEventListener('okBtnFn', function(){
        // 点击弹窗按钮，触发回调事件
      });
    }
  } </script>
```

## 2、组件样式覆盖

对于开发者来说，难免会遇到需要调整组件内部样式的时候。无论你是使用`antd`、`vant`还是使用其它组件库，但 WC 的 CSS 防污染机制导致你很难修改内部样式。这需要你付出一些代价来变相的修改内部样式，

## 3、组件内部资源相对路径问题

就目前来说，任何直接基于 Custom Element v1, Template 和 HTML Import 的组件都无法做到完全资源独立 —— 在不知道使用方环境且不给使用方增加额外限制的情况下使用内部封装的任何资源文件。比如如果你有一个自定义 icon 组件：

```
class MyIcon extends HTMLElement {
    static get observedAttributes() { return ['name','size','color'] }
    constructor() {
        super();
        const shadowRoot = this.attachShadow({ mode: 'open' });
        shadowRoot.innerHTML = `
            <svg class="icon" id="icon" aria-hidden="true" viewBox="0 0 1024 1024">
                <use id="use"></use>
            </svg>
        `
    }

    attributeChangedCallback (name, oldValue, newValue) {
        if( name == 'name' && this.shadowRoot){
            // 如果使用的项目中，根目录没有 icon.svg 文件，那就 gg
            this.use.setAttributeNS('http://www.w3.org/1999/xlink', 'xlink:href', `./icon.svg#icon-${newValue}`);
        }
    }
}

customElements.define('my-icon', MyIcon);
```

如果使用的项目中，根目录没有 icon.svg 文件，那就 gg。如果你在这里使用 cdn 路径，就会出现跨域问题。

## 4、form表单类组件 value 获取问题

Shadow DOM 中包含有 `<input>`、`<textarea>` 或 `<select>` 等标签的 value 不会在 form 表单中自动关联。

示例代码：
```/
/ web component
class InputAge extends HTMLElement {
  constructor() {
    super();
  }

  // connect component
  connectedCallback() {
    const shadow = this.attachShadow({ mode: 'closed' });
    shadow.innerHTML = `<input type="number" placeholder="age" min="18" max="120" />`;
  }
}

// register component
customElements.define( 'input-age', InputAge );
```

WC 组件被使用后

```
<form id="myform">
  <input type="text" name="your-name" placeholder="name" />
  <input-age name="your-age"></input-age>

  <button>submit</button>
</form>

<script> const form = document.getElementById('myform');

  form.addEventListener('submit', e => {

    e.preventDefault();
    console.log('Submitted data:');

    const data = new FormData(form);
    for (let nv of data.entries()) {
      console.log(`  ${ nv[0] }: ${ nv[1] }`);
    }

  }); </script>
```

提交的时候无法获取 `input-age` 的 `value`。当然会有解决方案，但会很复杂。

[点击查看](https://codepen.io/craigbuckler/pen/JjWmxwo)点击预览 

其它表单标签获取 value 解决方案参考：

![](https://upload-images.jianshu.io/upload_images/10024246-46f2e2d15a768738.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[点击查看](https://html.spec.whatwg.org/multipage/custom-elements.html)

## 5、其它

此外，缺少数据绑定和状态管理也是 WC 存在的缺陷，此处不再赘述。

## 写在后面

*   WC 指在丰富 HTML 的 DOM 特性，让 HTML 拥有更强大的复用能力
*   WC 可以直接当做原生标签，在任何前端框架和无框架中运行
*   结合当下的主流技术栈来说，WC 当前主要问题在于复杂的组件中，数据通信和事件传递存在一定使用成本
*   兼容问题，比如可以覆盖内部样式的 `:part` 方法
