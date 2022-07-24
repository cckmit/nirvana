---
title: 【译】应避免使用的TypeScript特性
date: 2022-04-12 16:28:03
categories: 前端
tags: [TypeScript]
---

## 正文

本文列举了四个建议避免使用的`TypeScript`特性。根据使用实际场景，你可能有很好的理由去使用这些特性，但是我们认为默认情况下不应该去使用它们。

`TypeScript`随着时间的推移，它已经发展成为一门复杂的语言。在其迭代发展早期，`TypeScript`团队增加了与`JavaScript`不兼容的特性。近期的迭代发展则更加保守，保持了与`JavaScript`特性更严格的兼容性。

和任何成熟的语言一样，我们必须做出艰难的抉择，决定使用哪些`TypeScript`特性，避免使用哪些`TypeScript`特性。我们团队在使用`TypeScript`构建`Execute Program`的后台和前端项目时，以及在创建我们全面的`TypeScript`课程时，亲身经历了这些权衡。根据我们的使用经验，以下是四个关于哪些特性需要避免使用的建议。

### 1\. 避免enum

`enum`给一组常量取了名字。在下面的例子中，`HttpMethod.Get`是字符串`'GET'`的一个名字。`HttpMethod`类型在概念上类似于字面量类型之间的联合类型，比如`'GET' | 'POST'`。

```
enum HttpMethod {
  Get = 'GET',
  Post = 'POST',
}
const method: HttpMethod = HttpMethod.Post;
method; // Evaluates to 'POST'
复制代码
```

下面是支持`enum`的论点：

假设我们后来有一天需要用`'post'`替换上面的字符串`'POST'`。我们只要把枚举的值改成`'post'`就可以了！系统中的其他代码仍然通过`HttpMethod.Post`引用该枚举成员，该枚举成员仍然存在。

现在设想一下同样的场景，用一个联合类型代替一个枚举。我们定义了联合类型`type HttpMethod = 'GET' | 'POST'`，后来决定将其修改为`'get' | 'post'`。任何试图使用`'GET'`或`'POST'`作为`HttpMethod`的代码现在都会出现类型错误。我们必须手动更新这些代码，与枚举相比较，这是一个额外的步骤。

这种关于`enum`的代码维护论点并不强。当在枚举或者联合类型中添加一个新成员时，我们很少会在创建成功后去修改它。如果我们使用联合类型，确实我们可能要花一些时间在多个地方更新相关代码，但是这并不是一个大问题，因为它很少发生。即使发生了，类型错误提示也会告诉我们应该做哪些代码更新。

枚举的缺点在于它们如何适配融入`TypeScript`语言中。`TypeScript`应该是加入了静态类型特性的`JavaScript`。如果我们从`TypeScript`代码中移除所有的类型代码，剩下的应该是有效的`JavaScript`代码。在`TypeScript`文档中使用的正式词是"type-level extension"（类型级扩展）：大多数`TypeScript`特性都是对`JavaScript`的类型级扩展，它们不影响代码的运行时行为。

下面是一段类型级扩展的具体例子，我们写下这段`TypeScript`代码：

```
function add(x: number, y: number): number {
  return x + y;
}
add(1, 2); // Evaluates to 3
复制代码
```

编译器会检查代码的类型。然后它会生成`JavaScript`代码。幸运的是，这一步很简单，编译器只需要删除所有的类型注释。在这个例子中，意味着删除`: number`。剩下的就是完全合法的`JavaScript`代码了。

```
function add(x, y) {
  return x + y;
}
add(1, 2); // Evaluates to 3
复制代码
```

大多数的`TypeScript`特性以这种方式工作，遵循类型级扩展规则。生成`JavaScript`代码，编译器只需要删除类型注释。

糟糕的是，枚举打破了这个规则。`HttpMethod`和`HttpMethod.Post`是一个类型的一部分，所以当`TypeScript`生成`JavaScript`代码时，它们应该被删除。可是，如果编译器只是简单地从我们上面的代码例子中删除枚举类型，我们仍然会留下引用`HttpMethod.Post`的`JavaScript`代码。这在程序执行过程中会出错：如果编译器删除了`HttpMethod.Post`，我们就不能引用它了！

```
/* This is compiled JavaScript code referencing a TypeScript enum. But if the
 * TypeScript compiler simply removes the enum, then there's nothing to
 * reference!
 *
 * This code fails at runtime:
 *   Uncaught ReferenceError: HttpMethod is not defined */
const method = HttpMethod.Post;
复制代码
```

在这种情况下，`TypeScript`的解决方案是打破自己的规则。当编译一个枚举时，编译器会发出额外的`JavaScript`代码，而这些代码在原始的`TypeScript`代码中是不存在的。很少有`TypeScript`特性是像枚举这样工作的，而且每加一个特性都给原本简单的`TypeScript`编译器模型增加了一个令人费解的复杂因素。基于以上原因，我们建议避免使用枚举，使用联合类型替代它。

为什么类型级扩展规则很重要？

让我们思索一下该规则如何与`JavaScript`生态、`TypeScript`工具互动。`TypeScript`项目本质上是`JavaScript`项目，所以它们经常使用像`Babel`和`Webpack`之类的`JavaScript`构建工具。这些工具是为`JavaScript`设计的，在今天这仍然是它们的主要聚焦点。每个工具都有自己的一个生态系统。有一个看似无穷无尽的`Babel`和`Webpack`插件的宇宙来处理代码。

`Babel`、`Webpack`、它们的许多插件以及所有生态系统中的其它工具和插件如何能够完全支持`TypeScript`呢？对于`TypeScript`语言中的大多数特性，类型级扩展规则使得这些工具的工作相对容易。这些工具只需剥离类型注释，留下有效的`JavaScript`代码。

而当涉及到枚举时（还有`namespace`，稍后会讲到），事情就变得比较困难了。简单地删除枚举是不够的，这些工具必须把`enum HttpMethod { ... }`转换为可以工作的`JavaScript`代码，尽管`JavaScript`中本来就没有枚举。

这给我们带来了`TypeScript`违反其自身类型级扩展规则的实际问题。像`Babel`、`Webpack`这样的工具以及它们的插件都是首先为`JavaScript`设计的，所以支持`TypeScript`只是它们众多功能中的一个。有时，支持`TypeScript`并没有得到像支持`JavaScript`那样的关注，这可能会导致bug的出现。

绝大多数工具在变量声明、函数定义等方面会做得很好；所有的这些都是相对容易处理的。但是有时错误会在枚举和命名空间中出现，因为它们需要的不仅仅是剥去类型注释。你可以相信`TypeScript`编译器本身能够正确编译这些功能，但是生态系统中一些很少使用的工具可能会犯错。

当你的`compiler`、`bundler`、`minifier`、`linter`、`code formatter`等静静地错误编译或者静静地错误解释你系统中的一个偏僻部分的代码时，可能会非常地难以调试。编译器错误是出了名的难以追踪。请注意这段话：“在这**一周**里，在**我的同事的帮助**下，我们设法对该错误的范围有了更好的了解。”

### 2\. 避免namespace

命名空间就像模块，只是一个文件中可以有多个命名空间。例如，我们可能有一个文件，为其导出的代码和测试定义了单独的命名空间。（我们不推荐这样做，但这是一个展示命名空间的简单方法）

```
namespace Util {
  export function wordCount(s: string) {
    return s.split(/\b\w+\b/g).length - 1;
  }
}

namespace Tests {
  export function testWordCount() {
    if (Util.wordCount('hello there') !== 2) {
      throw new Error("Expected word count for 'hello there' to be 2");
    }
  }
}

Tests.testWordCount();
复制代码
```

命名空间在实践中会引起问题。在上文关于枚举的部分，我们看到了`TypeScript`的类型级扩展规则。通常情况下，编译器会删除所有的类型注释，剩下的就是有效的`JavaScript`代码。

命名空间以与枚举相同的方式打破了类型级扩展规则。在`namespace Util { export function wordCount ... }`，我们无法删除类型定义。整个命名空间是一个`TypeScript`特定的类型定义！命名空间外的其它代码调用`Util.wordCount(...)`会发生什么？如果我们在生成`JavaScript`代码之前删除了`Util`命名空间，那么`Util`就不存在了，所以`Util.wordCount(...)`函数的调用是不可能工作的。

和枚举一样，`TypeScript`编译器不能简单地删除命名空间的定义。相反，它必须生成原始`TypeScript`代码中不存在的新`JavaScript`代码。

对于枚举，我们建议使用联合类型来替代。对于命名空间，我们建议使用常规模块。创建许多小文件可能有点繁琐，但模块具有与命名空间相同的基本功能，没有潜在的缺点。

### 3\. 避免装饰器（暂时性地）

装饰器是修改或者替换其它函数（或类）的函数。下面是一个取材自官方文档的装饰器例子。

```
// This is the decorator.
@sealed
class BugReport {
  type = "report";
  title: string;

  constructor(t: string) {
    this.title = t;
  }
}
复制代码
```

上面的`@sealed`装饰器就是暗指`C#`的sealed修改器，它可以防止其它类继承于被密封的类。我们会通过编写一个`sealed`函数来实现它，该函数接收一个类并对其进行修改以防止继承。

装饰器一开始是被添加到`TypeScript`中，后来才开始在JavaScript（ECMAScript）中的标准化过程。截至2022年1月份，装饰器仍然是ECMAScript的第2阶段提案。第二阶段是“草案”。装饰器提案似乎也陷入了委员会的炼狱：自2019年2月份以来，它一直处于第二阶段。

我们建议避免使用装饰器，直到它们至少是第三阶段（“候选”）提案，或者对于更保守的团队来说是第四阶段（“完成”）。

ECMAScript装饰器有可能总是永远不会被最终确定下来。如果这样的话，它们最终会陷入与`TypeScript`枚举和命名空间类似的情况。它们将一直违反`TypeScript`的类型级扩展规则，而且在使用官方`TypeScript`编译器以外的构建工具时，它们更有可能发生故障。我们不知道这是否会发生，但是装饰器的好处足够小，我们宁愿等待和观察。

一些开源库，最明显的是`TypeORM`，大量使用装饰器。我们意识到，遵循本文的建议就会排除使用`TypeORM`。使用`TypeORM`和它的装饰器是一个很好的选择，但是应该是有意为之的：因为认识到装饰器目前正处于标准化的炼狱中，可能永远不会被最终确定。

### 4\. 避免使用private

`TypeScript`有两种方式来使类的字段私有化。一种是旧的`private`关键字，它是`TypeScript`特有的。然后是新的`#somePrivateField`语法，它参考自`JavaScript`。下面是一个例子，分别展示了它们：

```
class MyClass {
  private field1: string;
  #field2: string;
  ...
}
复制代码
```

我们推荐新的`#somePrivateField`语法，原因很简单：这两个特性大致等同。除非有令人信服的理由，否则我们希望能与`JavaScript`保持功能上的一致性。

回顾一下我们的四个建议：

1.  避免使用枚举
2.  避免使用命名空间
3.  暂停使用装饰器，直到它们被标准化。如果你真的需要一个需要装饰器的库，在做决策时要考虑它们的标准化状态
4.  支持`#somePrivateField`而不是`private somePrivateField`

即使避免使用这些特性，对它们有一定的了解也是好的。它们经常出现在传统的代码中，甚至在一些新的代码中也有出现。并非每一个人都同意避免使用它们。

## 原文链接

[www.executeprogram.com/blog/typesc…](https://www.executeprogram.com/blog/typescript-features-to-avoid)
