# 为什么不应该再使用“var”

> 原文：<https://medium.com/hackernoon/why-you-shouldnt-use-var-anymore-f109a58b9b70>

## 以及'`let’`如何解决一个常见的 JavaScript“gotcha”…

![](img/e5976211ca3e4303d297e9d39afa449e.png)

我使用 ES2015 (ES6)语法编写 JavaScript 已经有一段时间了，并且已经开始欣赏许多优雅和简单的语言变化。我适应的第一个也是最容易的变化之一是使用`let` / `const`而不是`var`。然而，我认为`let`比`var`提供的好处理所当然；它不仅仅是一个华丽的新语法，而是提供了一个重要的作用域机制。

> 我认为`let`比`var...`提供的好处是理所当然的

首先值得注意的是，我声明的绝大多数变量最适合使用 `*const*`而不是`let` / `var`。这是因为在声明之后，如果试图改变它的值，那么`const`将抛出一个错误，这是一个防止意外突变的有用特性。然而，经常需要一个可变变量，特别是在循环场景中作为计数器。但是在这些情况下，如果它们都提供了所需的基本可变性，为什么要使用`let`而不是`var`?

简单的答案是`**let**` **提供了函数作用域`var`所没有的块作用域**。这个解释显然需要一个实际的例子。请允许我用一个经典的前端工程面试问题来展示这一重要区别:

在本例中，控制台中将打印什么内容？

```
var callbacks = [];
(function() {
  for (var i = 0; i < 5; i++) {
    callbacks.push( function() { return i; } );
  }
})();console.log(callbacks.map( function(cb) { return cb(); } ));
```

在这个例子中，我们循环了 5 次，每次都将一个函数推入到`callback`数组中。在用 5 个函数填充数组后，我们运行每个函数，将它们的结果记录到控制台。一个新手工程师可能会(错误地)回答结果是`[0, 1, 2, 3, 4]`，*一个合理的分析，但是却成为了 JavaScript“提升”陷阱*的牺牲品。

![](img/21a89e5746e81de35a5f53ea50482364.png)

Don’t be a victim. Avoid the hoisting trap with ‘let’!

正确答案实际上是`[5, 5, 5, 5, 5]`，当您考虑吊装在幕后做什么时，这是有意义的:

```
var callbacks = [];
(function() {
  **var i;**
  for (**i** = 0; i < 5; i++) {
    callbacks.push( function() { return i; } );
  }
})();console.log(callbacks.map( function(cb) { return cb(); } ));
```

请注意 JavaScript 如何将变量声明提升到函数块的顶部，导致在每个回调函数执行时,`i`的值为`5`,因为在调用这些函数之前,`for`循环已经完全递增。

有很多方法可以经典地解决这个问题，让脚本将`[0, 1, 2, 3, 4]`记录到控制台，但是`let`给了我们一个非常简单的解决方案:

```
var callbacks = [];
(function() {
  for (**let** i = 0; i < 5; i++) {
    callbacks.push( function() { return i; } );
  }
})();console.log(callbacks.map( function(cb) { return cb(); } ));
```

就是这样— **只需将** `**var**` **替换为** `**let**`，示例就可以正常工作了！这要归功于`let`的块范围行为。`*let*` *没有被提升到函数范围的顶部，而是停留在循环*的块范围内，导致每次迭代都有一个单独的`i`实例。

那么，你应该使用`var`吗？正如你可能已经从这篇文章的标题中推断出的，我认为你不应该使用 `**var**`。从技术上来说，`var`在您希望维护函数作用域而不是块作用域的情况下仍然有用，但是我坚信，如果您需要依靠不直观的提升来让您的脚本工作，您会遇到更大的问题😜。

**更新:**针对读者提出的两个最常见的讨论点:

1.  诚然`const`并不是*真正的*不可改变。例如:

```
const myNotQuiteImmutableObject = {
  thisCanBeChanged: "not immutable"
};myNotQuiteImmutableObject.thisCanBeChanged = "see I changed it.";
```

然而，这仍然阻止了基本突变，例如:

```
const immutableString = "you can't change me";immutableString = "D'OH!"; // error
```

如果你需要真正的不变性，去看看脸书优秀的不可变库。

2.`{mySuperAncientBrowser}`中不支持`let`这是真的，甚至[有些近的浏览器仍然不支持](http://caniuse.com/#feat=let) `[let](http://caniuse.com/#feat=let)`。这有一个显而易见的简单解决方案:使用[巴别塔](https://babeljs.io/)。Babel 将允许您使用 JavaScript 的所有最好和最新的特性，然后转换成一个即使简单的旧 IE8 也能理解的词汇表(有一些例外)。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)