# Python[3]非常适合大型代码库的项目

> 原文：<https://medium.com/hackernoon/python-is-great-for-large-code-base-992fa9233066>

人们总是建议 Java、C++、Go 或任何其他类型的语言，由于它们的静态类型特性，对于具有大代码库的项目，它们是通过 Python 静态类型化的。

当代码变长或变旧时，无论您应用了多少干净的代码或可读性原则，当您稍后要修改或修复一些错误时，它会变得令人讨厌和难以理解，重构变得有风险，代码审查时间增加，并且，您可能会开始对它产生怨恨。修改另一个对干净的代码或文档没有任何线索的懒人代码变得疯狂，你可能也开始对人类产生同样的怨恨。

Python 非常适合快速开发，可以将一个想法从零带到产品中，但是拥有动态类型的特性也有它自己的缺点。

好消息是，**这不再是真的了。**

Python 3.6 + [Mypy](https://github.com/python/mypy) 自带*类型*检查现在，关于类型检查你可以查看 PEP 526 及其之前的 PEP。

# 什么是 mypy？

Mypy 是 Python 的可选静态类型检查器。您可以使用 Python 3.5 中引入的类型注释标准( [PEP 484](https://www.python.org/dev/peps/pep-0484/) )向 Python 程序添加类型提示，并使用 mypy 对它们进行静态类型检查。甚至不用运行程序就能发现程序中的错误！

PyCharm 已经支持类型提示，甚至是类型注释语法，我们已经在产品中使用 Python 3.5 编写了项目，代码库完全使用这些新特性编写，代码具有惊人的可读性。

***“这使得代码审查和维护变得更加容易。”***

当我说大型代码库时，我主要指的是至少+100k 行的项目。

现在我正在处理多个项目，每个项目至少有大约 25 万行代码，而且它们的代码库还在增长。

我不是说不可能，只是没有提到类型，没有提示的时候更难。

当我审查代码时，我经常发现自己通读函数体只是为了找出函数的参数类型和返回类型。就像不断在脑子里运行代码，才能对它有一个清晰的理解。

我写了很多 DocString，Sphinx 风格或 Python 风格或任何其他类型的方式，让我的开发工具如 PyCharm 了解这些类型。

例如，你知道这个`fib`方法接受并返回吗？

```
def fib(n):
    a, b = 0, 1
    while a < n:
        yield a
        a, b = b, a+b
```

我相信您知道，因为您只是阅读了函数体来搞清楚这一点，但是有了新的特性，您就不再需要这样做了:

```
def fib(n: int) -> Iterator[int]:
    a, b = 0, 1
    while a < n:
        yield a
        a, b = b, a+b
```

更好更干净。

这是一个非常简单的例子来说明这个问题以及如何解决它。

你再也不能提出这个借口，说 Python 是为小型项目服务的”，这仅仅是因为动态类型的本质。

一些需要阅读的 pep:

*   https://www.python.org/dev/peps/pep-0526/
*   【https://www.python.org/dev/peps/pep-0484/ 
*   [https://www.python.org/dev/peps/pep-3107/](https://www.python.org/dev/peps/pep-3107/)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！