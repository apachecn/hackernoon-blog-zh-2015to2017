# 谁说 javascript 容易？

> 原文：<https://medium.com/hackernoon/who-said-javascript-easy-f4a1d5b399b8>

这里有一些 javascript 初学者应该知道的技巧和陷阱。如果你已经是一个专家了，请带着一种了解的表情阅读这篇文章。

![](img/b814b1105751cae5313d4dfc7c15abef.png)

Javascript is just another programming language. What could possibly go wrong ?

## 1.你尝试过对一组数字进行排序吗？

Javascript `sort()`默认使用字母数字排序。

所以`[1,2,5,10].sort()`会输出`[1, 10, 2, 5].`

要正确排序数组，可以使用`[1,2,5,10].sort((a, b) => a — b)`

简单的解决方案，前提是你首先知道有问题:)

## 2.《新约会》真是太棒了

`new Date()`能接受:

*   无参数:立即返回
*   一个参数`x`:返回 1970 年 1 月 1 日+ x 毫秒。Unix 的人知道为什么。
*   new Date(1，1，1)得出 1901 年 2 月 1 日。因为你知道，第一个表示 1900 年后的第一年，第二个是一年中的第二个月(因此是二月)，正常人会从索引 1 开始索引一个表，第三个显然是一个月的第一天，所以是 1，因为有时索引确实从 1 开始。
*   哦，还有新的日期(2016，1，1)不会把 2016 年加到 1900 年。它只代表 2016 年。

![](img/619a2248ed774234e22e988a08ed3f22.png)

## 3.替换不替换

我发现这是一件好事，因为我不喜欢改变输入的函数。你也应该知道`replace`只会替换第一个匹配:

如果您希望替换所有出现的内容，您可以使用带有`/g`的正则表达式:

```
"bob".replace(/b/g, 'l') === 'lol' // replace all occurences
```

## 4.小心比较

原因:[1，2，3]和[1，2，3]是两个独立的数组。它们只是碰巧包含相同的值。它们具有不同的引用，无法与`===`进行比较

## 5.数组不是基元类型

要知道你的 var 是不是数组，还是可以用`Array.isArray(myVar)`

## 6.关闭

这是一个众所周知的 javascript 面试问题:

你期望它输出 0，1，2…？你知道为什么没有吗？你会怎么解决？

让我们提一下这个问题的两个可能的解决方案:

*   用`let`代替`var`。嘣。解决了。

> “let 和 var 之间的区别在于作用域。`var`的作用域是最近的函数块，`let`的作用域是最近的包围的*块(如果在任何块之外，两者都是全局的)，它可以小于函数块。([源](http://stackoverflow.com/questions/762011/whats-the-difference-between-using-let-and-var-to-declare-a-variable) e)*

*   替代方案:使用`bind:`

```
Greeters.push(console.log.bind(null, i))
```

有很多其他方法可以做到这一点。这些只是我的前 2 个选择:)

## 7.说到 bind

你认为这会输出什么？

如果你认为这将与`Cannot read property 'name' of undefined`发生冲突，给你一分

原因:`greet`没有在适当的环境下运行。还是那句话，有很多方法可以解决这个问题。

*   我个人喜欢

这种方式可以确保使用您的类实例作为上下文来调用`greet`。

*   如果您觉得`greet`永远不应该脱离上下文，您也可以将它绑定到您的类构造函数中:

*   你也应该知道粗箭头(`=>`)可以用来保留上下文。这也是可行的:

尽管我发现最后一个例子在我们的例子中不够优雅。

![](img/722ed511f3b8d2405507b1cbecf02d9d.png)

I’m glad we fixed this

## 结论

恭喜，你现在可以把东西放到网上了。大概吧。甚至可能不破坏任何东西(尽管通常是这样)。干杯\o/

如果有什么我应该提到的，请告诉我！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)