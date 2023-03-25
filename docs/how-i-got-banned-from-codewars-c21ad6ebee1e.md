# 我是如何被 CodeWars 封杀的

> 原文：<https://medium.com/hackernoon/how-i-got-banned-from-codewars-c21ad6ebee1e>

大家好。

我是一名前 C 开发人员(5 年)，4 年前爱上了 Ruby 和它的生态系统。在练习了几个月 Ruby 之后，我辞掉了原来的工作，成为一家大公司的全职 RoR 开发人员，做了大量的网站抓取工作，并开发了一个大型的 rails 应用程序。

在那家公司工作两年后，我开始做大量的招聘工作，主要是采购和技术面试。

就在那时，我发现了 CodeWars([https://www.codewars.com](https://www.codewars.com))，并经常用它来做我的技术面试。我经常在网站上训练自己，解决了很多形。

和我的同事们一起，我们想挑战自己，目的是在周末赢得最多的 CodeWars 积分。有了两个孩子，很难找到集中注意力的时间，但我的夜晚有一段时间变短了！

在用“正常”的解决方案解决了最难的红宝石形之后*(brain fuck interpreter:*[*】https://www . codewars . com/kata/my-small-code-interpreter-aka-brainf-star-star-star-k*](https://www.codewars.com/kata/my-smallest-code-interpreter-aka-brainf-star-star-k)*，事物的建造者:*[*【https://www.codewars.com/kata/the-builder-of-things*](https://www.codewars.com/kata/the-builder-of-things)*等等…)* ，作为游戏中的竞争对手(主要是超级粉碎兄弟混战)，我有了

第三个周末，我突然想到了什么。我试图了解如何检查形的有效性，是的，我已经找到了一种方法来解决几乎任何形与一个相同的和真正简单的解决方案。

一个形是这样检查的:是我的解等于他们的解，还是他们的解等于我的解。

起初，我试图通过在我的方法中返回 nil 来解决这个问题，并以这种方式从 NilClass 中重载==运算符

```
class NilClass def ==(_)
    true
  endenddef mymethod
end
```

我点击了提交，然后“噗噗”，我解决了这个形。我告诉自己“哦，妈的，这是作弊吗？哦我真的不在乎，这是一个解决方案，我的朋友会被吹！”

然后我试着在网站上最大的形上做，一个 1 kyu。三向编译器。和以前一样，我尝试在数组或哈希类上使用附加费`==`方法，并再次“pfiout ”,我赢得了分数。

在这一点上，完了，我告诉自己，只要 rekt 我的同事。我制作了这个[https://github.com/Poilon/codewars-hack](https://github.com/Poilon/codewars-hack)，它将为我赢得代码战积分。

我找到了很多乐趣，然后做了这个。《无所事事》跻身 CodeWars 排行榜前 300 名。我确实打败了我的朋友，并且成功地赢得了我们的比赛！当然，我很想拿回我的帐户，即使是 0 分，只是为了回答评论，看看我自己提交的形上的新解决方案。

当然这是欺骗，但是如果有人在代码面试时这样做了，我想他会有很好的机会被录用！

露比是爱情，露比是爵士乐。❤巴黎。rb！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！