# Trae，另一个 HTTP 库

> 原文：<https://medium.com/hackernoon/trae-another-http-library-70000860a5f4>

**TL；DR；我们已经在使用 [fetch](https://developer.mozilla.org/en/docs/Web/API/Fetch_API) 和一些不太实用的助手了。**

不久前，当我在 React 应用程序上寻找使用什么来处理 HTTP 请求时，我发现了关于 T0 的信息。来自 jQuery 和 Angular，这是我从来没有担心过的事情，因为我会使用库/框架提供的东西，并对此感到高兴。

我对任何推动网络发展并使其变得更好的事情都感到非常兴奋。尤其是对于`**fetch**`，有很多理由让我们兴奋不已:

*   一个不错的 API。*在你面前*[*XMLHttpRequest*](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)*！*
*   请求/响应原语
*   `**'no-cors'**`不透明的反应
*   流
*   流阅读器和克隆
*   标题类
*   缓存控制
*   无凭证`**same-origin**`请求

你可以在杰克的帖子中深入了解他们。

# 采用提取

> 我们谈论的是 JS，你必须使用最新的东西，对吗？

玩笑归玩笑，在用一些概念验证进行了一点点尝试之后，我得到了在工作中使用它的绝佳机会。我开始了一个新项目，多亏了 Github 的 polyfill，我已经可以在那些还没有支持或者永远不会有支持的浏览器上安全地使用它了。太好了！

# 原生 API 缺点

现在`**fetch**`是一个原生 API，因此它的工作不是提供一个类似库的接口，带有助手和速记方法。用了一段时间后，最后有了一堆帮手。像这些:

这是每个请求的样子:

你可以说没什么问题，但感觉不对。我不想开始另一个项目，直接复制我的`**fetch-utils**`文件。我也不喜欢有一群帮手，这一点也不坏，但我知道我可以做得更好。

所以我把我的担心告诉了 Nico，他说“哟，我也有同样的问题！”。在讨论了一会儿一些想法，我们开始写一个库。

这就是我们如何以 trae 结束的。

![](img/a2705a28886f4d0cfe637f844f2c457c.png)

Cool, but…

**酷但是，和**[**axios**](https://github.com/mzabriskie/axios)**相比如何？**

不仅很多人问了这个问题，我们自己也做了。

作为一种默认的 HTTP 库，它确实影响了我们。浏览它的代码帮助我们理解了 HTTP 库的需求和内部结构，并改进和完善了我们自己的一些想法。如果你想阅读组织良好的、书面的和文档化的代码，那就去看看吧。我必须承认，我甚至是马特 T20 的秘密粉丝。

但是`axios`内部使用`XHMLHttpRequest`。如果你还没有，看看杰克的帖子，了解更多关于 T2 的信息，以及相对于 T3 有哪些改进。

除此之外，`trae`和`axios`一样，只是一个 HTTP 库。而且，*我在这里可能有偏见，但是，*我会说这是一个很好的。因此，如果你像我们一样对`fetch`感到兴奋，想要使用它，同时又想拥有一个库的好东西，那就试试`trae`。

# 小崔，这到底是什么意思？

在经历了从[爱马仕](https://en.wikipedia.org/wiki/Hermes)、[卡戎](https://en.wikipedia.org/wiki/Charon_(mythology)、 [Huginn 和 Muninn](https://en.wikipedia.org/wiki/Huginn_and_Muninn) 到名犬甚至[信鸽](http://www.pigeonracingpigeon.com/uncategorized/famouspigeons/)的名字之后，我们决定用一些简单甚至有趣的名字，`trae**.**` 在西班牙语中是[的意思](https://translate.google.com/#es/en/trae) *带来*，我们觉得对于一个专门获取数据的图书馆来说，这是一个很好的选择。

# 下一步是什么？

列表上有一些[要做的](https://github.com/huemul/trae#todo)项目，但是关于库本身，我们喜欢它的 API，并且认为，尽管它很年轻，但已经足够成熟，可以说我们不会对它进行大的突破性改变。

这并不意味着我们不会做出改进。我们每天都在使用`trae`,并且正在寻找新的方法让它变得更好。我甚至有*也许不那么疯狂*的想法，用[一堆很酷的东西](https://github.com/Huemul/trae-chrome-devtools)构建一个开发工具扩展。

我们对贡献是非常开放的，所以如果您发现有什么需要改进的地方，请随时提问，甚至提交一个拉动请求。

# 结论

每当你找到一种方法来改进你的工作流程，就更进一步，用它做点什么，不管是一个库，一个小模块还是一个工具。在这个过程中，你会学到很多东西，更好的是，你会帮助很多其他人。OSS FTW！！！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> 黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)