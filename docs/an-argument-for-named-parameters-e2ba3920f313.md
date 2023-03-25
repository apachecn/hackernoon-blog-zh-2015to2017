# 命名参数的参数

> 原文：<https://medium.com/hackernoon/an-argument-for-named-parameters-e2ba3920f313>

当在动态类型语言中处理大型和/或不熟悉的代码库时，我经常会遇到函数调用，如果不查看函数实现，就不知道哪个参数代表什么。让我们以下面这个电话为例-

```
rewardUsers(user, this, 'waitlist')
```

没错！你不知道这些参数意味着什么。如果我们通过引入一个许多编程新手都不熟悉的概念——命名参数——来改变一下局面，会怎么样呢？

```
rewardUsers(inviter: user, invitee: this, source: 'waitlist')
```

> 这是额外的 26 个字符！

那又怎样？我不算你看一眼就明白代码所节省的时间，但这比我打 26 个字的时间还多。

> 但是什么语言实际上支持命名参数呢？

为此，我将参考维基百科关于这个主题的文章，其中写道:

> 许多语言都明确支持命名参数:部分示例包括 Ada、C# 4.0+、ColdFusion、Common Lisp、Scala、Kotlin、Mathematica、PL/SQL、Python、R、Smalltalk、Fortran、Visual Basic、IDL、Swift 和 Objective C。

我发现 [C#的可选命名参数](https://msdn.microsoft.com/en-us/library/dd264739.aspx)非常有用。本质上，它允许您挑选何时使用命名参数，何时不使用，而无需做任何额外的工作。

```
class NamedExample {
    static void Main(string[] args) {
        Console.WriteLine(CalculateBMI(123, 64));
        Console.WriteLine(CalculateBMI(weight: 123, height: 64));
        Console.WriteLine(CalculateBMI(123, height: 64));
    } static int CalculateBMI(int weight, int height) {
        return (weight * 703) / (height * height);
    }
}
```

通常情况下，语言会发展到添加这样的特性。例如，Ruby 2.0 在语言中引入了关键字参数，如下例所示。

```
def calculate_total(subtotal:, tax:, discount:)
  subtotal + tax — discount
endcalculate_total(subtotal: 100, tax: 10, discount: 5)
```

幸运的是，即使在一些不支持*命名参数的语言中，您也可以自己实现它们。例如，ES6 增强的 Object Literal 和[destructing](https://hackernoon.com/tagged/destructuring)特性启发我开始在 [JavaScript](https://hackernoon.com/tagged/javascript) 中频繁地通过对象使用命名参数。让我们深入调查一下-*

```
function rewardUsers({ inviter, invitee, source }) {
  // ...
}rewardUsers({ inviter, source, invitee: this })
// ES5 equivalent-
// rewardUsers({ inviter: inviter, source: source, invitee: this)
```

在上面的例子中，我使用一个对象而不是单独的值参数来创建命名参数。ES6 引入的简写语法使得不重复变得更加容易。例如，如果您已经在作用域中定义了变量 *inviter* ，并希望将它传递给一个接受 *inviter* 作为其对象键之一的函数，则不需要编写 *{ inviter: inviter }* ，而只需编写 *{ inviter }* 。

其他语言，如 Elixir、Rust 和 Python，也支持析构，因此允许您创建命名参数。

> 什么时候应该使用命名参数？

每当引入新功能时，这是一个常见的问题。就我个人而言，如果可能的话，我几乎总是在设计带有多个参数的函数时使用命名参数。

利用命名参数提高代码可读性。只有我的 2。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！