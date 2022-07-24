---
title: WinterCG 社区正式成立，前端代码终于可以运行在后端了
date: 2022-05-28 16:28:03
categories: 资讯
---
WinterCG 社区正式成立，前端代码终于可以一次编写到处运行了？！
5 月 9 日，Cloudflare 在其官方博客宣布，将与 Node.js 和 Deno 开源项目的核心贡献者合作，成立一个新的社区组，命名为 WinterCG（Web-interoperable Runtimes Community Group），该项目汇集了三个最大的 JavaScript 环境，为开发人员提供了灵活性和选择，同时创建边缘计算的未来标准。通过一套通用标准，允许在 Node.js、Deno 和 Cloudflare 无服务器环境中编写可移植的应用程序，同时不再需要重写代码，实现“编写一次，随处运行”的承诺。

Cloudflare 联合创始人兼首席执行官 Matthew Prince 表示，“这是一个以前无法实现的壮举。JavaScript 正在被数以百万计的开发人员使用，也是一般开发者学习的第一种语言。到目前为止，JavaScript 标准完全集中在前端，比如浏览器。” Matthew Prince 补充道，“通过对核心 JavaScript API 的标准化，同时兼顾到前端和后端，这样可以授权前端开发者以一种熟悉的方式，更多更便捷地访问后端。”

针对这个消息，我们采访了 Deno 核心贡献者 justjavac（迷渡）老师，他表示：“这个社区早该成立了！这样可以让云计算或者边缘计算的平台提供和 Web 一致的 API，而不是各自开发自己的 API。对于社区开发者来说，不再需要额外学习一套 API，写一套代码就可以部署到不同平台。”

## WinterCG 社区成立的初衷是什么？
据 Cloudflare 官方说明， WinterCG 社区组的本质在于为 JavaScript 运行时提供一个空间，以便于在 API 互操作性方面进行协作。这种协作大致包括三个方面，分别是：运行时之间的讨论；现有规范社区（WHATWG、W3C）中的 Web API 的提案，包括现有提案和新提案；完善和维护现有运行时行为的文档。

对此，justjavac（迷渡） 认为，在 Node.js 发布的时候，还没有那么多的 Web API 规范，于是 Node.js 设计了一套服务器端 API，像 Deno 在设计之初就直接复用了 Web API，大部分 JavaScript 开发者都非常熟悉这些 API，比如 fetch、URL、TextEncoder 等。后来 WHATWG 制定了很多 Web API 规范，Node.js 的最近版本也开始添加了符合 WHATWG 规范的 API。

然而这些  Web API 又不能 100% 按照标准在服务器端实现，毕竟 Web 标准是为浏览器制定的，如果每个服务器端运行时都按自己的方式进行调整，最终的结果就是代码只能运行在特定的某一个平台上，这也是 Cloudflare 成立 WinterCG 的初衷。

不难看出，新成立的 WinterCG 将更加直接关注非 Web 浏览器的特定需求的实现，与目前专注于 Web 平台功能和 API 开发的现有社区组织形成了互补作用。WinterCG  的目标更加清晰：关注在后端服务器、无服务器计算、物联网、命令行工具等环境中实现这些相同的特性。

像 Cloudflare Workers 这样的无服务器环境，或者像 Node.js 和 Deno 这样的运行时，都有诸多广泛的问题与不同的需求，这些都与 Web 浏览器无关，反之亦然。最终，在开发各种规范时，这些差异性需求的脱节和缺失，就导致了一种情况——非浏览器运行时已经实现了它们自己定制的、临时的解决方案，并且已经运行在了各个生产环境中。

WinterCG 社区的成立就是为了改变以上问题，它提供了一个讨论和倡导所有 Web 环境的共同需求的场所，可以部署在堆栈的任何地方。对于开发人员来说，代码的可移植性非常重要，如果你写完一套代码，想要把它迁移到不同的环境中（例如，从 Node.js 到 Deno）去运行的话，你应该也不想完全重写一遍吧？

Cloudflare Workers、Node.js、Deno 和 Web 浏览器都有很大的不同，但它们共享了很多共同的功能。例如，它们都提供了用于生成加密哈希的 API；它们都以某种方式处理流数据；它们都提供了向某处发送 HTTP 请求的能力。如果存在重叠，并且需求和功能是相同的，那么环境都应该实现相同的标准化机制。

所以，WinterCG 通过制定一套通用标准的方式，让开发人员只需要关心他们编写的代码能够正常运行即可，而不管代码在哪里运行。

## WinterCG ：不打算发布一套独立的标准 API 集
据报道，新的 WinterCG 社区组将在 W3C 的既定流程下运行。

从小组的命名可以看出，关键点在于“web-interoperable”。据官方解释，此处使用的“web”的含义与 W3C 以及 WHATWG 社区使用的术语完全相同。确切地说法便是“web 浏览器”。因此，术语“web-interoperable”意味着以一种与 web 浏览器相同或至少尽可能一致的方式实现功能。例如，新的 URL() 构造函数在浏览器中的工作方式与新的 URL() 构造函数在 Node.js、Deno 和 Cloudflare Workers 中的工作方式完全相同。

对于 WinterCG 来说，承认 Node.js、Deno 和 Cloudflare Workers 明确不是 web 浏览器这一事实很重要。虽然这一点显而易见，但仍有必要指出，因为各种 JavaScript 环境之间的差异可能会极大地影响标准化 API 的设计决策。

对此，官方举例说明，Node.js 和 Deno 都提供对本地文件系统的完全访问。相比之下，Cloudflare Workers 没有本地文件系统；并且 Web 浏览器必然会限制应用程序操作本地文件系统。同样，虽然 Web 浏览器固有地包括一个网站“origin”的概念并实现 CORS 等机制来保护用户免受各种安全威胁，但在 Node.js, Deno 和 Cloudflare Workers 操作的服务器端却没有相同的“origin”概念。

到目前为止，W3C 和 WHATWG 都非常关注 Web 浏览器的需求。WinterCG 这一新的 Web 可互操作的运行时社区组将明确地处理并倡导每个人的需求。

对此，WinterCG 也表示，自己并不打算发布一套独立的标准 API 集。WinterCG 中发布的新规范的想法也会先提交给 W3C 和 WHATWG 进行考虑，以获取和达到更多的共识。但是，如果 Web 浏览器对其他环境 (如 Cloudflare Workers) 所需要的功能没有特别的需求，WinterCG 将被授权以自己发布的规范进行推进。前提约束是不会有意引入与已建立的 Web 标准相冲突或不兼容的内容。

## 最小通用 Web API
“最小通用 Web 平台 API 是标准化 Web 平台 API 的一个精心设计的子集，旨在定义浏览器和非浏览器基于 JavaScript 的运行时环境的通用功能的最小集合。”这是目前规范草案中的相关介绍。

换个说法来说：它是一组最小的现有 Web API，将在 Node.js、Deno 和 Cloudflare Workers 中一致且正确地实现。大多数 API（除了一些例外和细微差别）已经存在于这些环境中，因此剩下的大部分工作是确保这些实现符合它们的相关规范并且可跨环境移植。

WinterCG 表示，每当某个环境偏离 API 的标准化定义时 (比如 Node.js 对 setTimeout() 和 setInterval() 的实现)，就会提供描述这些差异的清晰文档。而这种差异应该只存在于与现有代码的向后兼容性中。

除此之外，WinterCG 目前已经开始起草 “Web Crypto Streams”的新规范，并提交给 W3C 进行考虑。Web Cryptography API 为常见的加密操作提供了一个最小并且非常有限的 API ，它的主要限制之一是与 Node.js 的内置 crypto 模块不同。Deno 是直接按 web crypto 规范实现的，而 Node 的内置 crypto 模块很早就开发完了，此次根据 Deno 和  Node.js 的现有实现制定规范，这为以后对其他平台的实现来说将更加方便与规范化。

针对目前 Node.js、Deno 和 Cloudflare Workers 实现 fetch() 的方式与在 web 浏览器中实现的方式有许多重要差异的问题，也为了使非 Web 浏览器环境更容易以一致的方式实现 fetch ，WinterCG 正在编写获取 fetch 的一个子集，专门处理那些不同的需求和约束。这个子集将与 fetch 标准完全兼容，并且由在 Node.js、Deno 和 Cloudflare Workers 中从事 fetch 工作的同一批人合作开发。这也不会成为 fetch 标准的竞争定义，而是一组关于如何在其他环境中正确实现 fetch 的文档化指南。

## WinterCG：我们才刚刚开始
WinterCG 表示，Web 可互操作的运行时社区组才刚刚起步，他们有许多雄心勃勃的目标。所有人都可以参与，所有工作都将通过 GitHub 的 https://github.com/wintercg 公开完成。目前，WinterCG 正在积极寻求与 W3C、WHATWG 和整个 JavaScript 社区的合作，以确保 Web 功能可用、始终如一地工作，并满足在堆栈中任何地方工作的所有 Web 开发人员的要求，它所做的一切都将以最大化互操作性为目标。

最后，感谢 justjavac（迷渡）老师的专业观点以及对本文的指导。

参考链接：

https://wintercg.org

https://github.com/wintercg/admin

https://blog.cloudflare.com/introducing-the-wintercg/

https://www.w3.org/community/wintercg/

https://deno.com/blog/announcing-wintercg