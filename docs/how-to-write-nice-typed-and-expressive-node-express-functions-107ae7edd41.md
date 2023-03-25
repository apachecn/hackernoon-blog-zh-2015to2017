# 如何编写漂亮的、有类型的、有表现力的节点表达函数

> 原文：<https://medium.com/hackernoon/how-to-write-nice-typed-and-expressive-node-express-functions-107ae7edd41>

![](img/130dc4f93197e43a3c074ccc0518c7b7.png)

在这篇文章中，我将展示一种包装节点表达控制器方法的好方法，以便有一个更好的自描述[代码库](https://hackernoon.com/tagged/codebase)。

假设您想要构建一个 api 端点来获取某个用户，并返回它。很简单，是吗？

## 1.很久以前

很久以前，人们会写一些可以阅读的东西

Old school style, with callbacks and no typings. The legend says that people actually found this very cool for some time.

## 2.现在

让我们现在到 2017 年，使用 typescript。首先，让我们利用这里的两个特性:

*   打字
*   异步/等待

代码可以重写如下:

注意`await user.findOne(...)`如何允许我们编码，就好像我们的流程是同步的一样。此外，任何异常(或拒绝的承诺)都将被主`try/catch`块捕获。对于更复杂的函数，已经有了很好的改进。如果你还不知道的话，[我碰巧对 async/await](https://hackernoon.com/await-is-the-new-black-e47217f96f06) 很感兴趣。

此外，尝试添加一个错别字，如`res.seeendStatus(500)`，typescript 编译器会警告您一个未知的方法。因为`Result`现在有打字了。很好。安全。

## 3.甚至更好？！

我用最后一个例子作为模板很长一段时间，它工作得很好。尽管如此，有些事情还是会困扰我。

通过查看这段代码，乍一看这个函数返回一个用户并不明显。添加一些文档是一个想法，但是你怎么能确定文档*准确地描述了下面的代码呢(通常的回答是:你不知道，所以你还是要读代码)？*

当然，这个例子很简单，所以你很快就能读懂这个方法。但是，当我们到处都在使用类型化的[函数](https://hackernoon.com/tagged/functions)时，为什么要读取方法呢？

我们在 [Hunteed](https://medium.com/u/4fb3c47e7f1c?source=post_page-----107ae7edd41--------------------------------) 想出的是一个包装器，它允许我们声明我们的 express 函数的输出。

在我们深入细节之前，这里有一个我们最终函数的例子:

在这里你可以注意到:

*   我们使用一些神奇的`apiMethod`装饰来减少样板文件。
*   第一行说明了一切:这个`wrappedShowUser`函数假装它返回一个包含类型为`User`的用户的对象，或者什么都不包含。
*   我们不写 no try/catch(仍然应该有一个 try catch，但是我们不需要每次都写它，冒着忘记它的风险)
*   该函数实际上返回我们假装发送的数据。typescript 可以检查我们做了什么。耶！

现在开始实施:

## 结论

正如所承诺的，我们最终的 api 方法现在如下所示

我真的认为这是一些可读和自描述的代码。它利用了类型、错误处理的优势，并保证没有人会忘记在 express api 端点上使用 try/catch。整洁！

![](img/260868264e5393acf53e1f87faa3b10f.png)

These people have absolutely no idea of what’s going on here, but they definitely look very happy about it.

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)