# C#7 开始看起来像函数式语言了吗？

> 原文：<https://medium.com/hackernoon/is-c-7-starting-to-look-like-a-functional-language-d4326b427aaa>

*本文摘自《C#* 中的 [*函数式编程》第一章*](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506)

![](img/975761ba443281b24113ba9f23ec0732.png)

许多程序员默认“你只能用函数式语言进行函数式编程(FP)”，考虑到 C#是一种面向对象的语言，尝试用 C#进行函数式编程是不值得的。

当然，这是一种肤浅的看法。如果你对 C#及其发展有更深入的了解，你可能知道 **C#是一种*多范例*语言**(就像 F#一样)，尽管它最初是一种命令式和面向对象的语言，**随着每个后续版本的发布，已经添加了许多功能特性，并且仍在添加。**

所以，这就引出了一个问题:C#对函数式编程的支持有多好？为了回答这个问题，让我首先澄清我所说的函数式编程是什么意思；也就是说，一个编程范例:

1.  强调函数的使用，并且
2.  避免状态突变

为了支持这种编程风格，语言必须:

1.  **支持一级值功能**；也就是说，必须能够像对待任何其他值一样对待函数；具体来说，您可以将函数用作其他函数的参数或返回值，或者将函数
    存储在集合中
2.  不鼓励就地更新(或者让就地更新变得不可能):默认情况下，变量、对象和数据结构应该是不可变的，创建对象的修改版本应该很容易
3.  **自动管理内存**:因为我们正在创建这样的修改副本，而不是就地改变数据，所以我们最终会创建更多的对象；这在没有自动内存管理的语言中是不切实际的

考虑到这一点，我们准备提出这个问题:

# C#语言的功能性如何？

嗯……让我想想。

1)函数在 C#中确实是一等价值。例如，
考虑以下代码:

```
Func<int, int> triple = x => x * 3;
var range = Enumerable.Range(1, 3);
var triples = range.Select(triple);triples // => [3, 6, 9]
```

这演示了作为第一类值的函数，因为我们可以将我们的
乘 3 函数赋给变量`triple`，并将其作为参数赋予`Select`。

事实上，从语言的最早版本开始，通过`Delegate`类型，C#就支持作为一级值的函数，随后引入的 *lambda 表达式*使得语法支持变得更好。

例如，对于类型推断(尤其是当我们想要将多参数函数作为参数传递给另一个函数时)，有一些奇怪的地方和限制；我将在第八章讨论这些。但是，总的来说，对作为一级值的函数的支持相当好。

2)理想情况下，我们也希望语言能够*阻止*就地更新。这是 C#最大的缺点:默认情况下一切都是可变的，程序员必须付出大量的努力来实现不变性。默认情况下，字段和变量都是可变的，必须显式标记`readonly`以防止变异。(与 F#相比，F #中的变量在默认情况下是不可变的，必须显式标记为`mutable`才能允许变异。)

类型呢？虽然框架中有一些不可变类型，
如`string`和`DateTime`，但是对用户定义的不可变类型的语言支持很差(尽管，正如您接下来将看到的，它在 C#6 中已经得到了改进，并且很可能在未来的版本中进一步改进)。最后，框架中的集合是可变的，但是不可变集合的可靠库是可用的。

3)另一方面，C#确实满足了自动内存管理这一更重要的需求。这意味着，尽管它不鼓励避免就地更新的编程模型，但由于垃圾收集，它支持这种编程模型。

总之，C#对*一些*函数技术、
有很好的支持，但对其他的没有。在其发展过程中，它已经有所改进，并将继续
改进对函数式技术的支持。

接下来，我们将回顾 C#过去、现在和未来版本中与 FP 特别相关的一些语言特性。

# LINQ 的功能性质

当 C# 3 和。NET framework 3.5，它包含了一系列受函数式语言启发的特性:这包括 LINQ 库(`System.Linq`)和一些新的语言特性，支持或增强你可以用 LINQ 做的事情，比如扩展方法和表达式树。

LINQ 为列表上的许多常见操作提供了实现(或者，更一般地说，在“序列”上，技术上应该叫“T5”)。其中最常见的是映射、排序和过滤。这里有一个结合了这三者的例子:

```
Enumerable.Range(1, 100).
   Where(i => i % 20 == 0).
   OrderBy(i => -i).
   Select(i => $”{i}%”)
// => [“100%”, “80%”, “60%”, “40%”, “20%”]
```

注意`Where`、`OrderBy`和`Select`如何将其他函数作为参数，并且不改变给定的`IEnumerable`，而是返回一个新的`IEnumerable`，说明了我上面提到的 FP 的两个原则。

LINQ 不仅方便查询内存中的对象(LINQ 到对象)，
还方便查询各种其他数据源，比如 SQL 表和 XML 数据。C#程序员已经将 LINQ 作为工作的标准工具集
，尤其是列表和关系数据(占典型代码库的相当大一部分)。从好的方面来看，这意味着您已经对函数库的 API 有了一些了解。

另一方面，当使用其他类型时，C#程序员通常坚持命令式风格，使用控制流语句来表达程序的预期行为。因此，我见过的大多数 C#代码库都是函数式风格(当使用`IEnumerable` -s 和`IQueryable` -s)和命令式风格(其他的)的拼凑。

这意味着，

> 虽然 C#程序员意识到了使用像 LINQ 这样的函数库的好处，但他们还没有充分接触到 LINQ 背后的设计原则，无法在自己的设计中利用这些技术。

这是本书旨在解决的问题。

# C#6 和 C#7 中的功能特性

虽然不像 C#3 那样具有革命性，但 C#6 和 C#7 带来了许多更小的语言特性，综合起来，为函数式编码提供了更好的体验和更惯用的语法。

**注意:**C # 6 和 C#7 中的大多数特性引入了更好的语法，*而不是*新功能。因此，如果你使用的是 C#的旧版本，你仍然可以应用本书中介绍的所有技术。然而，这些特性极大地提高了可读性，使函数式编程更具吸引力。

让我们在下面的清单中看看这些特性，然后讨论为什么它们与 FP 相关。

**清单 1。与 FP 相关的 C#6 和 C#7 特性**

```
using static System.Math;                          <1>public class Circle
{
   public Circle(double radius) 
      => Radius = radius;                          <2> public double Radius { get; }                   <2> public double Circumference                     <3>
      => PI * 2 * Radius;                          <3>

   public double Area
   {
      get
      {
         double Square(double d) => Pow(d, 2);     <4>
         return PI * Square(Radius);
      }
   } public (double Circumference, double Area) Stats   <5>
      => (Circumference, Area);
}
```

1.  `using static`允许无限制地访问`System.Math`的静态成员，如`PI`和下面的 Pow
2.  仅 getter 自动属性只能在构造函数中设置
3.  表达丰富的特性
4.  局部函数是在另一个方法中声明的方法
5.  C#7 元组语法允许成员名

## 用"`using static"`导入静态成员

C#6 中的`using static`语句允许我们“导入”一个类的静态成员(在这个例子中，是`System.Math`类)。因此，在我们的示例中，我们可以调用`Math`的`PI`和`Pow` 成员，而无需进一步限定。

```
using static System.Math;
//...public double Circumference
   => PI * 2 * Radius;
```

为什么这很重要？在 FP 中，我们优先考虑那些行为只依赖于其输入参数的函数，因为我们可以孤立地推理和测试这些函数(与实例方法相反，实例方法的实现依赖于实例成员)。这些函数在 C#中是作为静态方法实现的，所以 C#中的函数库将主要由静态方法组成。

允许我们更容易地使用这样的库，尽管过度使用会导致命名空间污染，但合理的使用可以产生干净、可读的代码。

## 具有纯 getter 自动属性的更简单的不可变类型

当你声明一个只支持 getter 的自动属性时，比如`Radius`，编译器会隐式声明一个`readonly`支持字段。因此，这些属性只能在构造函数或内联中赋值。

```
public Circle(double radius) 
   => Radius = radius;public double Radius { get; }
```

C#6 中的纯 Getter 自动属性有助于定义不可变类型。`Circle`类演示了这一点:它只有一个字段(`Radius`的后台字段)，就是`readonly`；所以，一旦创建，一个`Circle`就永远不能改变。

## 具有表达式主体成员的更简洁的函数

`Circumference`属性是用`=>`引入的“表达式体”声明的，而不是用`{ }`中常见的“语句体”。请注意，与`Area`属性相比，这要简洁得多！

```
public double Circumference
   => PI * 2 * Radius;
```

在 FP 中，我们倾向于编写许多简单的函数，其中许多是一行程序，然后将它们组合成更复杂的工作流。表达式主体方法允许我们以最小的语法干扰来完成这项工作。当我们想要编写一个返回函数的函数时，这一点尤其明显——这是我们将在本书中经常用到的。

表达式主体语法是在 C#6 中为方法和属性引入的，并在 C#7 中推广到也适用于构造函数、析构函数、getters 和 setters。

## 本地功能

编写大量简单的函数意味着通常只能从一个位置调用函数，而 C#7 允许我们通过在方法范围内声明方法来明确这一点；例如，`Square`方法是在`Area` getter 的范围内声明的。

```
get
{
   double Square(double d) => Pow(d, 2);
   return PI * Square(Radius);
}
```

## 更好的元组语法

这大概是 C#7 最重要的特点。它允许我们轻松地创建和使用元组，最重要的是，为它们的元素指定有意义的名称。例如，`Stats`属性返回一个类型为`(double, double)`的元组，但是另外指定了有意义的名称，通过这些名称可以访问它的元素。

```
public (double Circumference, double Area) Stats
   => (Circumference, Area);
```

元组在 FP 中重要的原因再次归结于将任务分解成非常小的函数的趋势。我们可能最终得到一种数据类型，它的唯一目的是捕获一个函数返回的信息，并期望作为另一个函数的输入。为这样的结构定义专用的类型是不切实际的，这与有意义的领域抽象不对应，这就是元组的用武之地。

# C#更具功能性的未来？

当我在 2016 年初撰写本章的第一稿时，C#7 的开发还处于早期阶段，有趣的是看到语言团队确定“强烈感兴趣”的所有功能都是通常与函数式语言相关的功能。其中包括:

*   记录类型(无样板的不可变类型)
*   代数数据类型(对类型系统的强大补充)
*   模式匹配(类似于对数据的“形状”起作用的“开关”语句，例如数据的类型，而不仅仅是值)
*   更好的元组语法

一方面，令人失望的是，只有最后一个项目能够交付。模式匹配的有限实现也已经发布，但是仍然与函数式语言中可用的模式匹配相差甚远，并且通常不足以满足我们在函数式编程时使用模式匹配的方式。

另一方面，这些特性仍然在未来版本的桌面上，并且已经在各自的提议上做了工作。这意味着我们可能会在 C#的未来版本中看到记录类型和更完整的模式匹配实现。

所以，C#已经准备好继续作为**的进化，一种多范例语言，具有越来越强大的功能组件**。你在这本书里学到的将会给你一个良好的基础来跟上语言和行业的发展，并且很好的理解语言未来版本背后的概念
和动机。

更多信息(和折扣代码！)下载免费的[第一章](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506)或者看这个[幻灯片演示](http://www.slideshare.net/ManningBooks/a-pragmatic-approach-to-functional-programming)。

[](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506) [## Manning |中的函数式编程

### 函数式编程是一种强调功能，同时避免状态突变的程序思维方式。它…

www.manning.com](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)