# 了解 ES6 常数

> 原文：<https://medium.com/hackernoon/understanding-es6-const-ab448906c4e1>

[ES6](https://hackernoon.com/tagged/es6) 给 [JavaScript](https://hackernoon.com/tagged/javascript) 语言带来了很多很棒的特性，其中之一就是[常量](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)。常数听起来很简单，是吧？

好吧，许多 JS 开发人员误解了它们…让我们浏览一下这个特性(以及更多)！

> 常数？不变的什么？

好问题！在 JavaScript 中，常量是**引用**中的常量。

> 参考？参考什么？

对一个**值**的引用。

```
‘hi’, 5, [], {} // all values
```

然而，重要的是要注意，对于对象(以及数组)，值是对对象本身的引用。

> 所以我就是不能改变一个“常量”的值？

**有点儿**。

这需要一堂关于 JS 类型的速成课。有五种基本类型:未定义、空、布尔、字符串和数字。其他的都是对象。第一等的是价值，而不是变量。而且，正如我前面提到的，一个对象的**值**仅仅是一个引用。按照设计(早在 ES6 之前)，所有原语都是**不可变的**，对象是**可变的**。

因此，当你“改变”(至少，看起来是)一个不可变变量(即 *++* )的值时，你实际上不是在变异，而是**将**重新赋值给一个全新的引用，因为**赋值**仅仅是创建所述“引用”的过程。

```
const num = 2;
num++; // TypeError
// *++* is just short-hand for *+=*, clearly assignment
```

然而，由于对象是可变的，所以您可以在重新赋值或不重新赋值的情况下更改它们的值。

```
const arr = [1, 2];
arr.push(3); // [1, 2, 3]
arr = [1, 2]; // SyntaxError (thus, value != reference)
```

并且，只有重新赋值-被 ES6 *const* ants 阻止，因为它处理的是引用，*而不是*值。

> 有什么好处？

常量主要有利于可读性，但最终也可能有利于性能。如果你知道一个值不会被替换，你可能更了解程序的意图。

经验法则——使用 [*const*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const) 除非你需要重新赋值——在这种情况下，你最有可能使用[*let*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)*来获得块范围的好处，但是我们将把它留到另一篇文章中。*

*使用常量以最小的努力提高代码的可读性，但是首先，要确保你理解它们！只有我的 2。*

*如果你觉得这很有趣，可以考虑在 [Twitter](https://twitter.com/tejasmanohar) 和 [GitHub](https://github.com/tejasmanohar) 上关注我，我在那里分享很酷的代码和更多。*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*