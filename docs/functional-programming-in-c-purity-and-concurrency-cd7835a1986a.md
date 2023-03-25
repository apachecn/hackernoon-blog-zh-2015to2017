# C#中的函数式编程:纯粹性和并发性

> 原文：<https://medium.com/hackernoon/functional-programming-in-c-purity-and-concurrency-cd7835a1986a>

*本文摘自《C#中的函数式编程》*[](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506)*第二章*

*![](img/e38769d6cbf91077e6ef9df41c13efde.png)*

*在本文中，我将使用一个简单的例子来说明为什么在为并行和更一般的并发执行编写代码时，**纯函数**应该是您的首选武器。*

> ***纯函数**是没有副作用(比如改变状态或者做 I/O)的函数，其输出严格由其输入参数决定。*

*下面是我们的简单场景:您希望将一个字符串列表格式化为一个编号列表，大小写应该是标准化的，每一项前面都有一个计数器。为此，我们希望创建一个 ListFormatter 类，其用法如下:*

```
*var shoppingList = new List<string> 
   { “coffee beans”, “BANANAS”, “Dates” };new ListFormatter()
 .Format(shoppingList)
 .ForEach(WriteLine);// prints: 1\. Coffee beans
// 2\. Bananas
// 3\. Dates*
```

*好的，这就是我们的要求。现在，让我们看看 ListFormatter 的一个可能的实现:*

***清单 1 结合纯函数和非纯函数的列表格式化程序***

```
*static class StringExt
{
   public static string ToSentenceCase(this string s) (1)
      => s.ToUpper()[0] + s.ToLower().Substring(1);
}

class ListFormatter
{
   int counter;

   string PrependCounter(string s) => $"{++counter}. {s}"; (2)

   public List<string> Format(List<string> list)
      => list
         .Select(StringExt.ToSentenceCase) (3)
         .Select(PrependCounter)           (3)
         .ToList();
}*
```

1.  *纯函数*
2.  *一个不纯的函数，因为它改变了全局状态*
3.  *纯函数和不纯函数的应用是相似的*

*关于纯度，有几点需要指出:*

*   ***ToSentenceCase** 是纯的(其输出严格由输入决定)。因为它的计算只依赖于输入参数，所以它可以是静态的，没有任何问题。*
*   ***PrependCounter** 递增计数器，所以是不纯的。因为它依赖于一个实例成员——计数器——所以我们不能让它成为静态的。*
*   *在**格式**方法中，我们使用**选择**将两个函数应用于列表中的项目，而不考虑纯度。这在未来的编程语言中可能会有所改变。*

*如果列表足够大，并行执行字符串操作有意义吗？作为一种优化，运行时可以决定这样做吗？我们接下来将处理这些问题。*

# *纯函数并行性很好*

*给定一个足够大的数据集进行处理，并行处理通常是有利的，特别是当处理是 CPU 密集型的并且数据片段可以独立处理时。*

*问题是纯函数和不纯函数的并行性不一样好，我将通过尝试用 ListFormatter 并行化我们的列表格式化函数来说明这一点。纯函数很好地并行化，更一般地说，不受使并发变得困难的问题的影响。*

*比较表达方式:*

```
*list.Select(ToSentenceCase).ToList()
list.AsParallel().Select(ToSentenceCase).ToList()*
```

*首先，我们使用在**枚举**上定义的**选择**方法，将纯函数 **ToSentenceCase** 应用于列表中的每个元素。第二个表达式非常相似，但是我们使用由并行 LINQ (PLINQ)提供的方法。*

***AsParallel** 将列表变成一个**并行查询**。因此， **Select** 解析为在 **ParallelEnumerable** 上定义的实现，它将把 **ToSentenceCase** 应用于列表中的每一项，但现在是并行的(也就是说，列表将被分割成块，然后将触发几个线程来处理每个块)。在这两种情况下，我们都使用 **ToList** 将结果收集到一个列表中。*

*正如您所料，这两个表达式产生相同的结果，但是一个是同步的，另一个是并行的。这很好:只需调用一次 **AsParallel** ，我们就可以几乎免费地获得并行化。*

*但这就提出了问题:为什么“几乎”免费？为什么我们不能免费拥有它？换句话说，为什么我们必须明确地指示运行时将操作并行化？为什么它不能发现并行化操作是个好主意，就像它能发现什么时候运行垃圾收集器一样？*

*答案是，运行时对所应用的函数了解得不够多，无法就并行化是否会改变程序流做出明智的决定。由于它们的属性，纯函数总是可以并行应用，但是正如我们指出的，运行时不知道所应用的函数的纯度，这就是为什么我预计未来的语言在这方面会有所不同。*

# *不纯函数的并行化*

*让我们看看如果我们天真地用不纯的 **PrependCounter** 函数应用并行化会发生什么:*

```
*list.Select(PrependCounter).ToList()
list.AsParallel().Select(PrependCounter).ToList()*
```

*由于 **PrependCounter** 增加了计数器变量，在并行版本中，我们将有多个线程同时读取和更新计数器。众所周知， **++** 不是一个原子操作，由于没有适当的锁定，我们将丢失一些更新，并以一个不正确的结果结束。*

*如果您用足够大的输入来测试这种方法，您将会得到如下的错误:*

```
*Expected string length 20 but was 19\. Strings differ at index 0.
Expected: "1000000\. Item1000000"
But was:  "956883\. Item1000000"
-----------^*
```

*如果你有一些多线程的经验，这听起来应该很熟悉。因为多个进程同时读写计数器，所以一些更新会丢失。您可能知道这可以通过在递增计数器时使用锁或互锁类来解决。但是锁定是一个必要的构造，我们在函数式编码时宁愿避免它。*

*让我们总结一下从上面学到的东西。与纯函数不同，纯函数的应用程序在默认情况下可以并行化，而非纯函数不会开箱即用地并行化。而且，由于并行执行是不确定的，所以在某些情况下，您的结果可能是正确的，而在其他情况下则不是(这不是我们想要面对的那种错误)。*

*意识到你的函数是否是纯函数可以帮助你理解和预防这些问题。此外，如果您在开发时考虑到了纯粹性，那么并行化执行将会更容易，如果您决定这样做的话。*

# *避免状态突变*

*除了锁定，避免并发更新陷阱的另一种方法是从源头上解决问题，也就是说，不要一开始就使用共享状态！如何做到这一点自然会因场景而异，但我将展示当前场景的一个解决方案，它使我们能够并行格式化列表。*

*让我们回到绘图板，看看我们是否能有一个不涉及突变的非平行解决方案。如果我们不是更新一个正在运行的计数器，而是生成一个我们需要的所有计数器值的列表，然后将列表中的项目与计数器列表中的项目“配对”会怎么样？*

*对于整数列表，我们可以使用 **Range** ，这是在**enumerate**上的一种方便的方法。*

***清单 2 生成一系列整数***

```
*Enumerable.Range(1, 3)
// => [1, 2, 3]*
```

*将两个并行链表“配对”的操作是 FP 中常见的操作，称为 **Zip** 。例如:*

***清单 3 用 Zip 组合来自并行列表的元素***

```
*Enumerable.Zip(
   new[] {1, 2, 3},
   new[] {"ichi", "ni", "san"},
   (number, name) => $"In Japanese, {number} is: {name}")

// => ["In Japanese, 1 is: ichi",
//     "In Japanese, 2 is: ni",
//     "In Japanese, 3 is: san"]*
```

*使用 Range 和 Zip，我们可以将列表格式化程序重写如下:*

***清单 4 列出了只使用纯函数的格式化程序***

```
*using static Enumerable;

static class ListFormatter
{
   public static List<string> Format(List<string> list)
   {
      var left = list.Select(StringExt.ToSentenceCase);
      var right = Range(1, list.Count);
      var zipped = Zip(left, right, (s, i) => $"{i}. {s}");
      return zipped.ToList();
   }
}*
```

*这里，我们使用应用了 ToSentenceCase 的列表作为 **Zip** 的“左侧”。“右侧”由**范围**构成。Zip 的第三个参数是“配对功能”:我们将对每一对物品做什么。*

***Zip** 可以用作扩展方法，所以我们通常会使用更流畅的语法来编写 **Format** 方法:*

```
*public static List<string> Format(List<string> list)
   => list
      .Select(StringExt.ToSentenceCase)
      .Zip(Range(1, list.Count), (s, i) => $"{i}. {s}")
      .ToList();*
```

*经过这次重构，**格式**是纯的，可以安全地变成静态的。让它平行怎么样？好了，现在这是小菜一碟，因为 PLINQ 提供了一个处理并行查询的 **Zip** 实现。列表格式化程序的并行实现如下所示:*

***清单 5:并行执行的纯实现***

```
*using static ParallelEnumerable; (2)

static class ListFormatter
{
   public static List<string> Format(List<string> list)
      => list.AsParallel() (1)
         .Select(StringExt.ToSentenceCase)
         .Zip(Range(1, list.Count), (s, i) => $"{i}. {s}")
         .ToList();
}*
```

1.  *将原始数据源转换为并行查询*
2.  *使用**平行数值**暴露的**范围***

*这几乎与非平行版本相同:只有两处不同。首先，我们将输入列表转换成一个 **ParallelQuery** ，这样之后的所有事情都是并行完成的。其次，我们使用静态更改了**，结果是我们将调用在 **ParallelEnumerable** 上定义的 **Range** 方法(这将返回一个 **ParallelQuery** ，这是 **Zip** 的并行版本所期望的)。其余的和非并行版本一样，而**格式的并行版本**仍然是一个纯函数。***

*在这种情况下， **ParallelEnumerable** 为我们做了所有繁重的工作，我们能够通过将我们的特定场景简化为压缩两个并行序列的更常见的场景来轻松解决这个问题——这种场景非常常见，在框架中已经解决了。*

*虽然在这个场景中，我们通过完全移除状态更新来实现并行执行，但是这样做并不总是那么容易。但是希望本文展示的思想能够让您在处理与并行性相关的问题时，以及更一般的并发性问题时，处于一个更好的位置。*

*更多信息(和折扣代码！)下载免费的[第一章](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506)或者看这个[幻灯片演示](http://www.slideshare.net/ManningBooks/a-pragmatic-approach-to-functional-programming)。*

*[](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506) [## Manning |中的函数式编程

### 函数式编程是一种强调功能，同时避免状态突变的程序思维方式。它…

www.manning.com](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> 黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)*