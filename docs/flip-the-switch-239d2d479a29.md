# 扳动开关

> 原文：<https://medium.com/hackernoon/flip-the-switch-239d2d479a29>

这些年来，我看过(也听过)很多 switch 语句是[害](http://ericleads.com/2012/12/switch-case-considered-harmful/)、[哑](http://debuggable.com/posts/code-insults-round-1---why-switch-blocks-are-dumb:4901d363-d210-482c-9794-65bd4834cda3)，甚至[臭](http://c2.com/cgi/wiki?SwitchStatementsSmell) (lol)的原因。我不喜欢讨厌某个特定的特性，道格拉斯·克洛克福特在他的传奇书籍 [*的第一章中解释了为什么:好的部分*](http://shop.oreilly.com/product/9780596517748.do) *-*

> 大多数[编程](https://hackernoon.com/tagged/programming)语言都包含好的部分和不好的部分。我发现，通过只使用好的部分，避免坏的部分，我可以成为一个更好的程序员。

*开关*声明是一个**合格的**特征。像大多数有能力的特性一样，它也有很多问题——需要在一个 *case* 中的每一条语句之后使用 *break* ,过程控制流，不合适的语法，代码块的处理，等等！不幸的是，解决这些问题需要我们重写它在核心的运行方式，规范会完全改变，这对于像 JavaScript 这样的语言来说，会产生大量的向后兼容性问题。我再次引用克罗克福德的话-

> 对于标准委员会来说，消除语言中的缺陷几乎是不可能的，因为这样做会导致依赖于那些不良部分的所有不良程序的崩溃。他们通常无能为力，除了在现有的不完美的基础上增加更多的功能。并且新的特征并不总是和谐地相互作用，因此产生了更多坏的部分。

![](img/3e091afabbd42cf945223dbe5a5af8b7.png)

那我们该怎么做呢？我们认为它是一个次优的特性，因为它比 JavaScript 开发人员可用的其他选项差，所以继续前进。今天，我们将研究这些优越的选择之一——对象文字。创建类似于*开关*的控制流的对象字面方法利用了散列表查找，并且显然使用了对象，JavaScript 语言的基础——我们将它们用于[一切](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript)！

让我们首先来看看这个基本的控制流，它使用一个 *switch* 语句来报告某种水果的价格。

```
function getPrice(fruit) {
  var price;
  switch (fruit) {
    case 'orange':
      price = 2.99;
      break;
    case 'apple':
      price = 1.24;
      break;
    case 'cherry':
      price = 3.55;
      break;
    default:
      price = 1.11;
  }
  return 'My fruit costs ' + price;
}console.log(get('apple')); // => My fruit costs 1.24
console.log(getPrice('apricot')); // => My fruit costs 1.10
```

现在，让我们用一个非常简单的对象来重写它。

```
function getPrice(fruit) {
  var fruits **=** {
    orange**:** 2.99,
    apple**:** 1.24,
    cherry**:** 3.55,
    default**:** 1.11
  };
  return 'My fruit costs' **+** (fruits[fruit] **||** items['default']);
}
```

这个已经干净了很多，也短了很多。我们可以通过跳过赋值并使用 *||* (OR)操作符作为默认值来进一步简化。最重要的是，永远不要忘记一次*再破*！

```
function getDrink(type) {
  return 'My fruit costs ' **+** ({
    orange**:** 2.99,
    apple**:** 1.24,
    cherry: 3.55
  }[type] || 1.11);
}// undefined || 1.11 === 1.11
```

到目前为止，您应该能够看出，在创建更清晰的控制流方面，对象文字优于 *switch* 语句。然而，我们也应该探索它们实际上如何更具可扩展性-

```
function getPrice(fruit) {
  var fruits = {
    orange: function () {
      return 2.99;
    },
    apple: function () {
      return 1.24;
    },
    cherry: function () {
      return 3.55;
    },
    default: function () {
      return 1.11;
    }
  };
  return (fruits[fruit] || fruits[‘default’])();
}
```

有时，我们可能需要将复杂的操作分配给控制流中的某个条件，事实上，我们可以将函数内联定义为对象文字的键。显然，在上面的例子中，一个函数是完全不必要的，但它足以证明这种能力。

在编写 JavaScript 应用程序时，使用对象文字来开发自然、干净和灵活的控制流。编码快乐！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)