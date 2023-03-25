# 姜戈太大，烧瓶太小，龙卷风刚刚好！

> 原文：<https://medium.com/hackernoon/django-too-big-flask-too-small-tornado-just-right-d5d002586bbc>

![](img/e6403a412c3586eb2422f69ef57fcb45.png)

在 Python web 世界中，当被要求构建一个 web 应用程序时，经常会在两个最流行的 Python web 生态系统 Django 或 Flask 之间进行选择。然而，有其他真正有能力的 web 框架可能会更好。 [Tornado](http://www.tornadoweb.org/en/stable/) 就是这些框架中的一个。龙卷风经常是我的秘密武器，因为它有一些独特的能力。它肯定比 Django 小，但是预装了许多小工具，让编写 Python web 代码变得很有趣。也许它适合你的下一个项目？以下是它的一些能力，让它在我的脑海中脱颖而出。

# 异步从你出生之前

由于 Node.js 和其他异步工具的兴起，AsyncIO 最近非常流行。Tornado 从一开始就是为了支持异步操作而构建的。所以在 Python 正式支持异步之前，Tornado 是在帮你写更干净的异步代码。它附带了许多实用程序来辅助它的异步操作，比如 IOLoop、协程、futures 等等。检查 Tornado 的[异步联网](http://www.tornadoweb.org/en/stable/networking.html)和[并发](http://www.tornadoweb.org/en/stable/coroutine.html)文档。所有的异步实用程序都可以在 Python 2 和 3 中工作。

# 本机 Websocket 支持

对于 Python 中的大多数 web 框架，websockets 是一个添加在中的特性。这使得他们的实现有时感觉有点笨拙或复杂。以 [Django 频道](https://channels.readthedocs.io/en/stable/index.html)为例。它可以用于一些非常高级的 websocket 和实时特性。所以 Django 频道让[“困难的事情成为可能”](https://www.amazon.com/gp/feature.html?ie=UTF8&docId=7137)。但是如果你只是需要一个简单的 websocket，Django Channels 是一个非常复杂的库，可以在你的应用中加入一些实时功能。借助 Tornado 的实施，it [“让简单的事情变得简单”](https://www.amazon.com/gp/feature.html?ie=UTF8&docId=7137)。这在某些情况下非常有用，因为您最初并不太关心向外扩展。这并不是说，你不能做到这一点，只是龙卷风让你实现 websockets 非常容易和简单。然后你可以在此基础上添加。相比之下，Django Channels 提供了更高级的功能，但也需要更多的设置和代码。

# 第三方登录支持

Tornado 自带对[社交登录](http://www.tornadoweb.org/en/stable/auth.html)的支持。我发现这真的很方便，因为对于较小的 web 应用程序，如果我需要一个登录系统，我喜欢使用第三方登录。这让我可以外包一大块我不想担心的应用程序。

# CLI 选项和配置文件解析

很多时候，当我需要编写 web 服务时，我希望包含一些命令行选项，或者有解析配置文件的选项，或者两者都有。Tornado 还包括内置的[选项/配置文件实用程序](http://www.tornadoweb.org/en/stable/options.html)。同样，这也使得在配置选项中编写 web 服务和打包变得非常方便，使得它可以像其他常见的命令行工具一样运行。

# 其他的东西

Tornado 还附带了您期望在 web 框架中看到的其他东西，如模板、路由、cookie 实用程序、请求解析等。我还发现，虽然它不是最流行的框架，但它仍然有一个很好的生态系统，人们支持它并构建使用它的库。所以为你的下一个项目检查一下吧。也许它会是最合适的？