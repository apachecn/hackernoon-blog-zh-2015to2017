# 介绍吊装困境！

> 原文：<https://medium.com/hackernoon/introducing-the-hoisting-dilemma-329969e5c826>

众所周知，JavaScript 对作用域的理解是*怪异的*。在大多数基于 C 语言的语言中，变量在声明的地方被实例化。然而，在 JS 中，标准变量**声明**通过一个称为提升的过程被移动到顶部。

在深入探讨之前，我们先花点时间来建立两个经常被新手开发者不当互换的流行术语的区别——**声明**和**初始化** *。*

**申报**

```
var x;
```

**初始化**

```
x = 5;
```

既然这个已经不碍事了，那就来说说你来这里的真正目的吧… **吊装**！在下面的例子中，我们在变量 *x* 被声明之前使用它。这是 JavaScript 提升的一个例子。

```
x = 7; // Assign 7 to x
var x; // Declare x
console.log(x); // => 7
```

够简单吗？好吧，吊装并不像你一开始想象的那样稳定。

```
var z = 3; 
(function() { 
  console.log(z); // => undefined
  var z = 4; 
})();
```

> 未定义？*但是之前！什么？啊？*

没错。*未定义*确实。尽管局部变量 *z* 像预期的那样被自动提升到了函数作用域的顶部(在日志语句的上方)，但是在日志语句执行之前，它的值仍然没有被定义，因为在 JavaScript 中只提升了声明，而不是初始化。

现在你明白了为什么这会令人困惑，并且会在不知不觉中导致简单的错误！但是不用担心，我们可以很容易地解决这个问题。

这个故事(ES2015 之前，另一篇文章即将发表)的寓意是通过在顶部声明所有变量来简化事情。

如果你觉得这很有趣，可以考虑在 [Twitter](https://twitter.com/tejasmanohar) 和 [GitHub](https://github.com/tejasmanohar) 上关注我，在那里我会分享一些很酷的代码。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)