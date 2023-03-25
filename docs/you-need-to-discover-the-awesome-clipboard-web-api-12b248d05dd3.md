# 如何仅用 JavaScript 复制到用户的剪贴板

> 原文：<https://medium.com/hackernoon/you-need-to-discover-the-awesome-clipboard-web-api-12b248d05dd3>

戴上你的学习帽，我们将进入另一个你今天之前可能不知道的 API！

![](img/4c1a92d25509b6821e9fbcbbdc785592.png)

It will test your head… and your mind… and your brain, too.

# 老实说，这不是一个真正的 API。

剪贴板 API 由多个 API 组成。除此之外，我们还需要引入更多与剪贴板及其实用程序不直接相关的 Web API，以便充分利用浏览器的剪贴板功能。到最后，你应该有几个新的工具，使你的网站和应用程序更容易处理文本！

## **从小处着手:剪贴板事件**

clipboard 事件是最容易使用的，从技术上讲，它是通过真正的 clipboard API 创建的。因此，您只能通过`copy`、`cut`和`paste`事件的事件处理程序来访问它。

这意味着您可以修改这些事件中发生的事情。您的代码可以改变最终出现在用户剪贴板上的内容，也可以改变最终显示在屏幕上的内容。然而，你不能创建你自己的事件，[除了在 Firefox](http://caniuse.com/#search=clipboardevent) 中。

**所以..在这些限制下，我们能做些什么呢？**

事实证明，很多！

我能想到一些修改这些事件的理由，我将在下面给出例子。

*   从复制或剪切的文本中添加或删除某些内容。
*   获取粘贴的数据，并在应用程序的其他地方使用。

这不一定是很多，但是通过一些额外的 API，我们可以增强我们的能力。稍后会详细介绍。

**让我们来看一些例子。**

This won’t really stop people from copying your text, but it shows how it overrides the initial value.

在上面的示例中，您所要做的就是向 copy 事件添加一个 eventListener。您接收的事件包含`clipboardData`及其三种方法:`setData`、`getData`和`clearData`。

它实际上拥有[数据传输](https://html.spec.whatwg.org/multipage/interaction.html#datatransfer)对象的所有方法和属性。因此，如果您不确定对`setData`或`getData`使用什么参数，您可以检查属性`items`和`types`。

Don’t let the user paste into the confirmation input, or don’t let them paste their own password.

这个例子展示了不允许用户将文本粘贴到 ID 为“passwordConfirmation”的输入中的情况。它也不允许他们将“用户密码”粘贴到任何字段中(我们假装这实际上是他们的密码。请暂时忽略一点，您永远不应该在您的 web 应用程序中以纯文本形式提供用户密码。).

另一个例子可能是脏话过滤器，如果你正在创建一个年轻用户的 web 应用程序。

你可以在你的应用程序的其他地方继续使用从`getData`获取的值。举个例子，也许你需要记录数据粘贴的时间，比如在一个考试应用程序中。有时在这种设置下粘贴文本可能没问题，但您可能希望能够稍后返回并确保粘贴的内容不是从其他地方复制的。

# 创建您自己的拷贝事件。

> 将文本复制到剪贴板应该不难。它不应该需要几十个步骤来配置或加载数百千字节。但最重要的是，它不应该依赖于 Flash 或任何臃肿的框架。—[clipboardjs.com](https://clipboardjs.com/)

在我们开始之前，我想向您介绍一个开源项目，它简化了我将要向您展示的一切。它被称为“clipboard.js ”,它允许你轻松地将文本复制到你的剪贴板。你可能会发现这比你自己去做要容易得多。这是一个相当小的库，有一个非常简单的 API。

[](https://clipboardjs.com/) [## 剪贴板. js

### 将文本复制到剪贴板应该不难。它不应该需要几十个步骤来配置或数百 kb 来…

clipboardjs.com](https://clipboardjs.com/) 

但是我们是来学习的，所以让我们看看如何仅使用 Web APIs 将文本复制到用户的剪贴板。

由于`ClipboardEvent`对象在大多数浏览器中是不公开的，我们必须走一条不同的路。为了实现这一点，我们需要增加我们的工具，`document.execCommand()`。这将允许我们运行一个`copy`命令，将当前选择复制到键盘上。

这里有一个用 CodePen 展示的例子:

这必须由用户事件触发，比如点击。这是在重要操作上实现的安全功能，例如打开文件上传窗口、复制到剪贴板等。它有助于保护用户安全，防止网站在他们不希望的时候与他们的计算机进行交互。

一些注意事项:

*   您可以使用类型属性设置为 text 的`input`，也可以使用`textarea`。后者创建它只需要少一行代码。
*   你可能会读到`document.execCommand`需要`designmode`，但据我所知这不是真的。它只需要由用户发起的事件来触发。
*   您可能希望将`document.execCommand`包装在一个 try/catch 块中。在一些浏览器中，它会在失败时抛出一个错误。
*   你可以检查`execCommand`的结果，看看它是否工作。它返回一个与命令成功相关的布尔值。

就是这样！您现在成功地将文本复制到您的剪贴板！

请随时查看我关于 API 的其他一些文章:[电池](https://hackernoon.com/javascript-apis-battery-c72baa74c203#.u4mh0vg6h)、[控制台](https://hackernoon.com/javascript-apis-console-23ebce270419#.cheoig9tu)和[视频](/@justindanielfuller/javascript-apis-video-api-db803f9fd1b7#.f1y9dbzuk)。

另外，请继续关注我即将发布的关于[设计模式](https://developer.mozilla.org/en-US/docs/Web/API/Document/designMode) API 的帖子，以及它如何与`execCommand`一起使用来做一些非常棒的事情！

嗨，我是贾斯汀·富勒。很高兴你看了我的帖子！我需要让你知道，我在这里写的一切都是我自己的观点，并不代表我的雇主。所有代码样本都是我自己的，与美国银行的代码完全无关。

我也希望收到您的来信，请随时通过 [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/) 、 [Github](https://github.com/justindfuller) 或 [Medium](/@justindanielfuller) 与我联系。再次感谢阅读！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)