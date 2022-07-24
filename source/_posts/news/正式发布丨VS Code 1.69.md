---
title: 正式发布丨VS Code 1.69
date: 2022-07-15 16:48:55
categories: 资讯
---
欢迎来到 Visual Studio Code 6月更新！本次更新主要亮点如下：

## ▌3 way merge editor

在这个版本中，我们继续开发 3 way merge editor。可以通过将 git.mergeEditor 设置为 true 来启用此功能，并将在未来的版本中默认启用。合并编辑器允许您快速解决 Git 合并冲突。启用后，可以通过单击源代码控制视图中的冲突文件来打开合并编辑器。复选框可用于接受和组合Theirs 或 Yours 中的更改：

[图片上传失败...(image-dd9437-1657896819481)]

合并编辑器中提供了所有语言功能（包括诊断、断点和测试），您可以立即获得有关合并结果中任何问题的反馈，结果也可以直接编辑。请注意复选框如何按预期更新：
[图片上传失败...(image-fd56f9-1657896819481)]

关闭合并编辑器或接受合并时，如果没有解决所有冲突，则会显示警告。合并编辑器支持字级合并。无论何时，您也可以手动解决冲突。
[图片上传失败...(image-f55bc0-1657896819481)]

## ▌Command Center

Command Center现在可以试用了。通过 window.commandCenter 设置启用它。命令中心取代了正常的标题栏，您可以快速搜索项目中的文件。单击main section以显示带有您最近的文件和搜索框的快速打开下拉菜单。
[图片上传失败...(image-b063fa-1657896819481)]

右侧还有一个按钮，可通过“？”显示快速访问选项。左侧是 Go Back 和 Go Forward 按钮，用于浏览您的编辑器历史记录。

## ▌“请勿打扰”模式

新的“请勿打扰”模式在启用时会隐藏所有非错误通知弹出窗口。进度通知将自动显示在状态栏中。隐藏的通知仍然可以在通知中心查看。
您可以通过打开通知中心（选择状态栏右侧的铃铛图标）并单击斜线铃铛图标来切换“请勿打扰”模式。
[图片上传失败...(image-5c8a66-1657896819481)]

## ▌Shell integration

自 1 月发布以来一直处于预览状态的 PowerShell、bash 和 zsh 的 Shell 集成现已停止预览！我们计划在 1.70 版本中默认启用它。要启用 shell 集成功能，请检查 Terminal > Integrated > Shell Integration : 在设置编辑器中启用或在 settings.json 中设置值：

<pre class="hljs language-json" style="box-sizing: border-box; font-family: var(--bs-font-monospace); font-size: 0.875em; direction: ltr; unicode-bidi: bidi-override; display: block; margin-top: 0px !important; margin-bottom: 1.25rem; overflow: auto; color: rgb(36, 41, 46); background: rgb(233, 236, 239); padding: 1rem; max-height: 35rem; line-height: 1.5; position: relative; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;">"terminal.integrated.shellIntegration.enabled": true</pre>

Shell 集成允许 VS Code 的终端更多地了解 Shell 内部发生的事情，来启用更多功能。Shell 集成的目标之一是使其能够在需要零配置的情况下工作。这是通过在启用设置时利用 shell 参数和/或环境变量自动将 shell 集成脚本“注入”到 shell 会话中来实现的。在某些情况下这不起作用，例如：在sub-shells或一些复杂的 shell 设置中，但我们也为那些更高级的情况提供了手动安装路线。

*   [Shell integration提供的功能摘要](https://link.segmentfault.com/?enc=JinHPDnROtulbDOzRMtnUg%3D%3D.6IPi15Jm%2FttoCt89%2FwBMRxEOsx1MR9VOLTRoQW70LaplabuJMlQdkwt3HbUIts%2BELUeIbJzFLIIb0YTAFy8shbVAtH1WYnYbIBXtargMT%2F0%3D)

## ▌Decorations

几次迭代之前，我们为终端缓冲区和概览标尺添加了decorations，这要归功于 Shell integration功能，以改进终端的导航。Decorations现在还标记任务的points of interest，可以使用命令导航功能（Ctrl/Cmd+Up、Ctrl/Cmd+Down）跳转到。对于启动/停止任务，任务开始旁边会出现一个decoration，并根据运行的退出代码（如果有）进行样式设置。
[图片上传失败...(image-c28cd3-1657896819481)]

[图片上传失败...(image-eb4ccf-1657896819481)]

## ▌为Git存储库添加Commit "操作按钮"

在 1.61 版本中，为 Git 存储库添加了发布和同步更改“操作按钮”。在这个里程碑中，我们添加了一个 Commit 按钮，该按钮具有主要操作和一组辅助操作。可以使用 git.postCommitCommand 设置控制辅助操作，并允许您在提交后执行推送或同步。

添加 Commit“操作按钮”后，有一个新设置 git.showActionButton，您可以使用它来控制源代码控制视图中显示的 Git 操作按钮。您仍然可以使用通用 scm.showActionButton 设置全局禁用任何操作按钮的可见性。

[图片上传失败...(image-9491a0-1657896819481)]

## ▌Step Into Target UI优化

一些调试器允许在某一行暂停时直接进入特定的函数调用。在这次迭代中，我们为此改进了 UI：

右键单击源行上的目标区域并选择 Step Into Target 将自动进入目标区域（如果有的话）

Command Palette 中有一个新命令 Debug: Step Into Target 可用，快捷键是 Ctrl+F11

*   [更多Debugging相关优化](https://link.segmentfault.com/?enc=lR1cS4%2FR630REM%2FR0YWM2Q%3D%3D.MWAX6bvJrASpv34OLwNgVRoV9mhwbHJ7ZMRT0rpFmYUx6gK%2BWHRiwDNDtKMeyYSgIE5emL8q%2FmBa5iFJIJF0Fopd6jbLylmq9Um%2BgUpYb0yIsvCnO0WHOBtWUen4l3dN)

## 本次更新还有一个重磅发布

#### VS Code Server (private preview)

在 VS Code 中，我们希望您能够无缝地利用使您的工作更高效的环境。VS Code 远程开发扩展（VS Code Remote Development extensions）允许您在 Windows Subsystem for Linux （WSL)、通过 SSH 的远程计算机，以及直接从 VS Code 开发容器中工作。这些扩展在远程环境中安装服务器，允许本地 VS Code 与远程源代码和运行时顺利交互。

我们现在提供独立的“VS Code Server”的private预览版，它是基于远程扩展使用的同一底层服务器构建的服务，以及一些额外的功能，例如：交互式 CLI 和促进与 vscode.dev 的安全连接， 而无需 SSH 连接。

[图片上传失败...(image-ae8987-1657896819481)]

我们的最终目标是无论您的项目存储在哪里，都可以增强您使用的代码 CLI，以打开 VS Code 的桌面和 Web 实例。我们正在为此积极努力，VS Code Server 是一个伟大的里程碑，我们希望获取您的反馈！
