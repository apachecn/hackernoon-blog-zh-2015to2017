# Javascript 中的惰性求值

> 原文：<https://medium.com/hackernoon/lazy-evaluation-in-javascript-84f7072631b7>

> TD；用 Object.defineProperty()可以很容易地执行 Javascript 对象属性中的 DR、Lazy 求值。

前一段时间，我在 **node.js** 中寻找一种达到某种**懒惰评估**的方法。

我正在构建一个对象，它必须有一个包含同类实例的属性(递归地)。这可以通过函数而不是属性来实现，但是这是一个非常特殊的场景。

所以我真正需要的是只有在调用 object 属性时才运行这个实例化的方法，而不是在对象构造期间。

尽管如此，我还是在 *stackoverflow* 上找到了一些关于*按名称调用或按需求调用*策略的 Javascript 文章，但没有一篇文章对我想要做的事情有效。

我遇到的大多数文章、代码和库都是通过一个[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)来实现**惰性评估**。但是这种对象在旧版本的 [node.js](https://hackernoon.com/tagged/nodejs) 中是没有的，我的情况就是这样。

简而言之，惰性评估，也称为按需调用，定义为:

> *按需要调用*是按名字调用的[记忆](https://en.wikipedia.org/wiki/Memoization)版本。

但是什么是点名呢？那记忆化呢？

## 按名称调用和按值调用

首先，让我们检查这两个函数:

```
// Evaluates with call-by-name strategy
1 function callByName (a, b) {
2  if (a === 1) {
3    return 10
4  }
5  return a + b
6 }// Evaluates with call-by-value strategy
1 function callByValue (a, b) {
2  if (a === 1) {
3    return 10
4  }
5  return a + b
6 }
```

请记住，这不是 Javascript。第一个函数通过按名称调用来计算，第二个函数通过按值调用来计算。

为了理解这两种类型的评估策略之间的区别，我们可以逐行查看一些关于如何执行的伪代码。

```
1 > callByName (1, 2 + 3)
2 > a === 1
3 > return 101 > callByValue(1, 2 + 3)
1 > callByValue(1, 5)
2 > a === 1
3 > return 10
```

你可能对第二个很熟悉。这是因为 Javascript 只将参数作为值传递(如果是对象，则通过引用传递)。

在**按值调用**函数(或*急切求值*)中，数字 2 和 3 首先相加，然后作为函数参数传递，值为 5。

另一方面，在示例中所示的**按名称调用**函数(或*惰性评估*)中，由于在函数使用 **b** 之前函数返回 10，所以第二个参数 **b** 甚至没有被评估。

## 记忆化

简而言之，*记忆化*是一种存储预计算结果(缓存)的优化技术，以避免对相同的输入进行重新计算。

以更正式的方式，这意味着如果一个幂等函数

```
**f(x) = g(x) + 1**
```

会不会在接收到一个输入 **v** 时总是输出某个值 **y** ，那么 **f(v) = y** 。

因此，如果 **g(v)** 在时间或机器资源方面非常*昂贵*，我们可以将结果 **y** 存储在缓存中，当 **v** 是输入时返回，这样我们就不必再次重新计算 **g(v)** 。

```
**cache = {}****cache[v] = y**
```

## 无限递归问题

现在我们将浏览一些非常简单的递归对象的代码，它进行惰性求值以避免无限递归。

我们将分析一种称为 Stream 的无限数据结构，这在函数式编程语言中非常常见。

如果我们尝试运行这段代码会发生什么？

```
var stream = new Stream(10);> RangeError: Maximum call stack size exceeded
```

不出所料，它会以**范围错误**失败。

问题出在下面的代码行，我们正在做一个*急切加载**下一个**属性**的***。

```
this.next = new Stream(value + 1);
```

我们把流改成*懒*吧。

注意，我还在流中添加了一个 **takeUntil** 方法。让我们继续，尝试运行这段代码。

```
var stream = new Stream(10);console.log(stream.takeUntil(20))> [10, 11, 12, 13, 14, 15, 16, 17, 18, 19]
```

现在，我们不仅可以构建流，而且还可以用这个实现运行*无限*算法，比如 **takeUntil** 。

你可能会想:为什么我们在这里没有使用*记忆*？

嗯，我们可以，但是这个*优化*是没有价值的，因为我们只是在做一个基本的数学运算，将两个操作数相加。这在计算上并不昂贵，例如，计算**值**是否是质数。

参考资料:

*   [http://stack overflow . com/questions/6605640/JavaScript-by-reference-vs-by-value # 6605700](http://stackoverflow.com/questions/6605640/javascript-by-reference-vs-by-value#6605700)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Object/define property](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)
*   [https://en.wikipedia.org/wiki/Evaluation_strategy](https://en.wikipedia.org/wiki/Evaluation_strategy)
*   [https://en.wikipedia.org/wiki/Memoization](https://en.wikipedia.org/wiki/Memoization)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！