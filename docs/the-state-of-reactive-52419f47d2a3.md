# 反应的状态

> 原文：<https://medium.com/hackernoon/the-state-of-reactive-52419f47d2a3>

有一些关于反应式编程的讨论。我准备了一些流行的 FRP 库之间的小比较

下面的一切都很主观:)

**RxJS**

*优点:*

*   文档和大社区。
*   与 Rx*系列共享接口。这是一个很大的优势，因为您可以采用来自 RxJava 社区的样本。
*   库中的许多原语和一些如何使用它们的示例
*   背压
*   它是一种用于其他库的标准具

*缺点:*

*   有时它看起来过于复杂
*   实现时考虑了“Java”。IMHO，像调度器这样的概念对于 [Node.js](https://hackernoon.com/tagged/nodejs) 是陌生的

**Bacon.js**

*优点*

*   非常好的文档，带有一些高级示例(例如贪吃蛇游戏和实现)
*   很多原始人
*   实现时考虑了 JS
*   我发现开始真的很容易

*缺点*

*   它没有实现反压力，这使得它在后端很傻

**Highland.js**

*优点:*

*   它旨在与 node.js(或 browserify)环境一起工作，这真的很好
*   没有陌生的概念(从 [JavaScript](https://hackernoon.com/tagged/javascript) /Node.js 的角度)
*   背压
*   实现是在节点流之上的，这真是一个好主意
*   占地面积小

*缺点*:

*   愚蠢的文档，没有例子。事实上，每次我试图从 Rx 或培根中提取样本时
*   缺少一些原语。实际上你可以自己实现大部分已经实现的功能，但是这很痛苦(见上一点)

**总结:**因为没有样本，你会全部学会:)依我看，最有前景的是 Highland.js .但是 RxJs 要成熟得多

如果你需要在 StackOverflow 上问一些问题，用 RxJS 方言翻译给 youth

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！