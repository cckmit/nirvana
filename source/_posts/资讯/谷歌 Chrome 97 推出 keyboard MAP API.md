---
title: 谷歌 Chrome 97 推出 keyboard MAP API
date: 2022-01-02 16:28:03
category: news
---
北京时间 1 月 5 日（美国时间 1 月 4 日晚些时候），谷歌官方正式发布了 Chrome 97 稳定版本浏览器，并已开始通过浏览器的自动更新系统推出。值得关注的是，此次 Chrome 97 新版本发布时更新的一个键盘 API（keyboard MAP API ）引起了不小争议。
据悉，此前由于 keyboard MAP API 无法在 iFrame 中使用，所以之前的一些 web 体验者也没法用这个功能，微软的 Office web 应用程序等应用程序也无法利用 API 检测键盘布局上的按键（键盘布局因地区或语言而异）。

而此次 Chrome 97 版本更新的 API，使得 iFrame 内的 web 应用程序可以使用该功能，也就是说 keyboard MAP API 可以获得用户的键盘布局，进一步跟踪和识别用户，因此也引发了不小争议。

对此，谷歌官方专门对实施该 keyboard MAP API 新功能做了解释：

getLayoutMap（）与代码结合使用，解决了使用不同布局图（如英语和法语键盘）识别键盘中按下的实际键的问题，但由于 getLayoutMap（）并非在所有上下文中都可用（不能在 iFrame 中使用），比如说 Excel、Word、PowerPoint 等 Office web 应用程序，这些在 Outlook Web、Teams 等里面显示为嵌入式体验并在 iFrame 中运行的应用都无法使用此 API。但只需要将 Keyboard MAP 添加到允许属性列表中即可解决这个问题。

尽管谷歌方面做了解释，但目前业内不少浏览器“对家”们却都坐不住了，纷纷发声明“抵制”。

其中，Mozilla、苹果、Brave 及其他浏览器的开发者们都对此事表示了担忧。这些公司反对浏览器集成的一个关键论点是：网站或可将该功能用于指纹识别目的。

苹果方面在 GitHub 上发布的一份回复中声明称“Keyboard MAP API 公开了一个高熵fingerprinting surface。从安全隐私方面而言，是不可被接受的，因此苹果公司的 WebKit 团队对实现目前提议/规范的这一功能不感兴趣。”

Brave 浏览器的制造商 Brave Software 表示：Brave 继承了 Chrome 实现的键盘 API，但不向用户提供任何功能（仅 Chrome 和 Opera 支持，但没有任何站点实际使用），且同样对该 API 可能被用于指纹识别表示担忧。

WICG Keyboard Map Draft 提到，API可用于指纹识别：使用不常见 ASCII 布局（如Dvorak或Colemak）的用户——使用 ASCII 布局的用户与其所在区域的默认布局不匹配。

Mozilla 公司则直接将该 Chrome 97 的 Keyboard MAP API 添加到了“有害API”列表中，并强调他们绝对不会在 Firefox web 浏览器中使用这些 API。

现在，谷歌方面宣布将在 Chrome 97 浏览器中使用该 API，但许多其他基于Chrome 的浏览器却表示不会支持该 API，或直接禁用该 API，事情变得“焦灼”了起来。

还记得上次，谷歌在 Chrome 94 更新后因引入了空闲检测 API 功能而引发争议。这一次，谷歌又在 Chrome 97 中引入了 Keyboard MAP API 再次引起争议。