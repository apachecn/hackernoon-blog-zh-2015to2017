# 为什么我们在 JS 中重写 Lua

> 原文：<https://medium.com/hackernoon/why-we-rewrote-lua-in-js-a66529a8278d>

有一种潜在的愿望，在浏览器中使用 JS 以外的东西。是否有保证本身就是另一个话题。但是你不需要搜索很长时间就能找到各种旨在在浏览器中引入另一种语言的项目。

Web Assembly 的到来重新点燃了我们许多人的渴望。但是 wasm 离可用于生产还有很长的路要走，并且仍然不允许与所有的 web apis 完全交互。

# Lua 非常适合浏览器

Lua 是一种简单的语言，只有很少的概念需要理解，语法清晰易读。你可以在几个小时内熟练掌握它。然而，它提供了非常有用的功能。仅举几个例子:

*   一级函数和闭包
*   一种通用的数据结构:[表](http://www.lua.org/pil/2.5.html)
*   Vararg 表达式
*   词法范围
*   迭代器
*   协程程序(见下文)

它有一个理智的强制系统(我在看着你 JS！).没有人在每个版本中添加新的概念，这使得它成为一种稳定可靠的语言。

[Lua](https://hackernoon.com/tagged/lua) 已经在服务器端成功使用，令人敬畏的 [OpenResty](https://openresty.org/en/) 为像 [itch.io](https://itch.io/) 这样的网站提供动力。甚至有一些很棒的 web 框架，如[lapi](http://leafo.net/lapis/)。

Lua 可以在许多不同的环境中找到，因为它很容易嵌入。多亏了 Lua C api，你可以用它写一个完整的程序，或者简单地把它作为一种*胶合*语言。

# 协同程序

浏览器的主要卖点之一是协程。协程完美地解决了编写异步代码的问题。不再有承诺，发电机等等。您可以像编写常规代码一样轻松地编写异步代码。

这里有一个简单的例子(全功能版本[这里是](https://gist.github.com/6a1cc45e36dd10afae30744f9b8193ac)):

使用 async/await 的类似版本可以是:

这很接近，但是注意在 Lua 版本中，你不需要在`async`函数中。在 JS 中，你必须经常有意识地选择是否标记一个函数`async`。在 Lua 中，**任何**函数都可以被中断，只要它运行在协程中。Bob Nystrom 写了一篇关于它的很棒的文章[在这里](http://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/)。

当你用 JS 编写异步代码时，你实际上是在堆积函数调用。这些回调都保留它们的 upvalues，这会导致高内存使用率。使用协程，您的函数实际上可以被挂起和恢复，而不必从闭包的金字塔中下降，因为它们运行在单独的 lua 状态中。

# 在浏览器中使用 Lua 的现有方式

已经有一些项目在某种程度上在浏览器中使用 Lua:

*   [月光](http://moonshinejs.org/)是 JS 中 Lua 的再实现。可悲的是，它不再被积极开发了。
*   [同一作者的星光](http://starlight.paulcuth.me.uk/)是 Lua to JS transpiler。不幸的是，用这种方法无法有效地实现协程。
*   [lua.vm.js](https://daurnimator.github.io/lua.vm.js/lua.vm.js.html) 使用 Emscripten 将原来的 lua 实现移植到 js。它速度很快，运行良好，但是它与 JS 的互操作性受到这样一个事实的影响，即我们最终用两个垃圾收集器来管理同一个内存。

# 丰加里

[**Fengari**](http://fengari.io/) (希腊语中的月亮)是用 [Javascript](https://hackernoon.com/tagged/javascript) 编写的 Lua VM，以浏览器为主要目标。

Fengari 的目标是 100%符合最新的 Lua 语义。它尽可能接近 Lua 的代码库，如果你熟悉它，你会很容易理解 Fengari。目前已经覆盖了 Lua 范围的 99% ,所以只要稍加调整，你就可以运行任何 Lua 项目。

有了 C API(而不是 JS API)，您可以决定用 Lua 编写任何东西，或者将它用作调用 JS 代码库来完成繁重工作的高级语言。这也意味着您可以将您的代码隔离在单独的隔离 Lua 状态中。

您还可以使用 [fengari-interop](https://github.com/fengari-lua/fengari-interop) 模块毫不费力地直接从 Lua 代码与 JS 端进行交互。它确保操作 JS 对象或函数总是按照您期望的方式进行:

你在 [fengari.io](http://fengari.io/) 上看到的 REPL 本身就是用 Lua 写的[，JS 只用来创建 Lua 状态和运行主脚本。](https://github.com/fengari-lua/fengari-web-cli/blob/master/src/web-cli.lua)

Fengari 仍在开发中，欢迎任何[反馈](https://github.com/fengari-lua/fengari/issues)！