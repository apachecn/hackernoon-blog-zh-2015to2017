# 一个小小的潜入，和对榆树的排斥

> 原文：<https://medium.com/hackernoon/a-small-dive-into-and-rejection-of-elm-8217fd5da235>

**我已不再使用介质。新地址是 kodare.net

我总是对前端开发的状态不满意。我对 ClojureScript 做了相当深入的研究，发现它最初很好，但最终在一些关键领域有所欠缺。我也在 JQuery 和 Angular land 中完成了我的工作(并尝试了几个小时试图让 Ember 工作，但认为这已经足够了，因为它在开始时非常讨厌)。那些显然比 ClojureScript + reagent 差多了。

所以这让我自然而然地想到了榆树。我听说过一些很棒的东西:很棒的错误消息、不会导致运行时错误的很好的类型安全、函数式编程的优点、超快的渲染等等。我一心一意要为埃尔姆做一个强有力的例子，以取代安格尔的工作。我带着(毫无根据的)坚定信念来到这里，认为这最终是可以接受的，甚至是好的。

我把工作中最简单的角背页作为起点。我的想法是，我可以制作一个 Elm 版本，然后在代码量、可读性等方面与现有代码进行比较。该页面是一个超级简单的日志查看器，其 GUI 基本如下:

> [搜索字段]时间范围:[3 选框]页面大小:[3 选框][复选框]详细程度
> 
> [带标签和其他东西的结果列表]

似乎很容易？事实证明，这是非常讨厌的，足以让我拒绝榆树。我就开门见山了。我喜欢 Elm 的地方:

*   错误信息大多非常好
*   编译器够快(这是高赞！)
*   榆树建筑很棒

现在是交易破坏者:

没有合理的或公认的方法来做选择框。你可能会问“什么？！这难道不是很基本的东西吗？”我认为你是对的。我越深入这个问题，我的结论就越坚定:榆树是:

*   不适合严肃使用
*   这种语言似乎不仅从 Haskell 继承了好的类型和很大程度上 ok 甚至好的语法，而且还继承了一种顽固的方法，即更喜欢(想象的)纯粹性而不是实用性，这导致了可能有用但实际上无法使用的核心语言特性

让我们检查一下这个问题。选择框包含选项列表，这些选项

*   是独一无二的(无论如何对于大多数用途来说)
*   有一个面向用户的字符串
*   有一些内部 ID

Elm 有所谓的联合类型，它非常好地模拟了第一点:

```
type TimeRange = AllTime | OneWeek | OneDay
```

从中创建一个面向用户的字符串也很不错:

```
timeRangeDisplayName : TimeRange -> StringtimeRangeDisplayName timeRange =
  case timeRange of
    AllTime -> “All time”
    OneWeek -> “One week”
    OneDay -> “24h”
```

这里的好处是，如果我向 TimeRange 定义添加一个替代项，我会得到一个编译错误，因为 timeRangeDisplayName 并不详尽，而且我没有默认情况。太好了！它非常冗长，并且重复了很多名称，显示名称的定义与类型定义相差甚远，但是对于 100%确定已经涵盖了所有情况来说，这是一个不错的折衷。

现在是内部 id。所以我得再做一个如上的函数。是的，我甚至不能得到不同选项的数字。我不能在编译或运行时遍历它们，没有检查，什么都没有。好吧，我认为，这很糟糕，但我会使用相同的面向用户的字符串，这很好。

现在我必须从这里创建 DOM。同样:没有办法在编译或运行时枚举选项，所以复制粘贴是:

```
 , select [id "id_time_range", onSelect ChangeTimeRange]
    [ option [] [text "24h”] — or should I use timeRangeDisplayName here? It’s even worse!
    , option [] [text "1 week"]
    , option [] [text "All time"]
    ]
```

呃。好吧，随便。很可能没事。然后当用户改变选择时，我需要更新我的模型。经过大量的谷歌搜索，我找到了这个:

```
onSelect : (String -> msg) -> Attribute msgonSelect msg =
    on “change” (Json.map msg targetValue)
```

首先，json 在这里做什么？好吧，好吧。类型签名？我不知道为什么会是这样，对我来说没有意义。然后，我得到一条带有目标值的消息。好了，酷，有进展了…现在把字符串转换回联合类型。

所以我基本上声明了上面的反函数:

```
timeRangeFromString : String -> TimeRange
timeRangeFromString s =
  case s of
    “All time” -> AllTime
    “One week” -> OneWeek
    “24h” -> OneDay
```

显然这是一个编译错误，因为它并不详尽。所以我添加了一个永远不会使用的默认值。整个功能基本就是复制粘贴。好吧。但问题是:如果我给 TimeRange 添加一个选项，这个函数就会变成默认值。没有办法让一个 case 必须产生所有有效的 _outputs_ 但是有一个方法可以检查你是否已经处理了所有 _inputs_。所以现在除了大量的复制粘贴之外，我还有脆弱的代码。

好吧，我就写个测试吧！但是我不能列举时间范围。:(

我询问了 Elm slack，人们的“解决方案”似乎是在列表中复制 union 类型:

```
type TimeRange = AllTime | OneWeek | OneDay
ranges = [AllTime, OneWeek, OneDay]
```

但是我不能确保这个列表是同步的，因为我不能枚举 TimeRange。

我在谷歌上搜索人们是如何处理这类事情的，发现了一个关于引入宏的激烈争论，因为人们正在编写代码生成器。在这一点上，我停下来，决定这种语言没有实用主义。

也许是时候再次在网上研究 python 了？

写完上面的文字后，我看到了这篇文章:[http://reasonablypolymorphic.com/blog/elm-is-wrong](http://reasonablypolymorphic.com/blog/elm-is-wrong)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！