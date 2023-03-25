# 函数式编程是一个谎言

> 原文：<https://medium.com/hackernoon/functional-programming-is-a-lie-d269c4240da2>

或者是对长生不老药的不满。

当你第一次开始[学习](https://hackernoon.com/tagged/learning)关于长生不老药的知识时，你知道状态和副作用是邪恶的，每个函数都应该是“纯粹的”，严格地接受输入，严格地返回输出，而不改变过程中的任何事情。然后，你学会了这个:

```
iex(20)> IO.puts "the very first lie"
the very first lie
:ok
```

`IO.puts/1`获取一个字符串并返回一个原子`:ok`，它有一个明显的大副作用，写给`stdout`。为什么这种情况下可以这样？我们不知道。为什么不是严格的功能性的，让`IO.puts`接受两个参数，一个终端输入/输出状态的表示和我们想要打印的字符串，在我们实际运行程序时推迟实际的打印？不管它有多不实用，它都会更实用。但是，为什么不呢？如果 [functional](https://hackernoon.com/tagged/functional) 在其他情况下有用，那么这样工作在这里也应该有用。如果不切实际，我们如何定义什么应该是纯功能的，什么应该有一个更实用的方法？缺乏关于这个特定主题的指导方针无助于理解在哪里划线。我认为这种缺失只是显示了我们对函数式编程的无知。

所以，现代函数式编程的格言是，对任何与 IO 相关、与文件相关、与显示相关或与数据库相关的东西来说，没有函数是可以的，但是我们会保持程序内部的函数性。除了发电机。这是可行的。然而，当我们编写的程序至少要与其中一个交互时，这意味着你的程序的大部分都将不起作用。而且，我们想要这些副作用，否则我们的程序就没用了。另一种方法是:让、logs、stdout 或 db 变量与每个函数一起传递，以避免不明确的副作用，这不是我们想要的，因为这简直是地狱。

以 Phoenix 为例，它是一个基于灵丹妙药的 web 框架。他们正在尝试后一种方法。他们将一个对象`conn`传递给每一个函数。不惜一切代价避免全球状态。`conn`包含所有请求数据和所有响应数据。所以，你会有这样的东西:

```
def index(conn, _params) do
    categories = Repo.all(Category)
    render(conn, "index.html", categories: categories)
end
```

请注意，数据库层不会传递给函数以及文件系统和记录器数据。为什么没有这样的东西:

```
def index(conn, db, fs, logger, _params) do
    categories = Repo.all(db, Category)
    render(conn, db, fs, logger, "index.html", categories: categories)
end
```

可能因为不务实不合理吧。也许全球国家是一种不可避免的邪恶。我们不想仅仅为了没有副作用而传递大量的变量。

函数式[编程](https://hackernoon.com/tagged/programming)，面向对象[编程](https://hackernoon.com/tagged/programming)，命令式和声明式编程，它们本身都很有趣，并且带来了让我们成为更好的程序员的深思熟虑的概念。这是个大秘密。好的程序员无论如何都会写出好的代码。糟糕的程序员无论如何都会产生不可读的代码。试图通过向每个人强加某种神奇的编程范式来让糟糕的程序员变得更好是一种幻想。它从来没有成功过，是我们行业中最大的谎言。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！