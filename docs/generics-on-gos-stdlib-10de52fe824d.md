# Go 标准库中的泛型

> 原文：<https://medium.com/hackernoon/generics-on-gos-stdlib-10de52fe824d>

所以最近 [Russ Cox](https://twitter.com/_rsc?lang=en) 在 [Gophercon](https://www.youtube.com/playlist?list=PL2ntRZ1ySWBdD9bru6IR-_WXUgJqvrtx9) 做了一个关于 *"* [*围棋的未来*](https://www.youtube.com/watch?v=0Zbh_vmAKvk&index=4&list=PL2ntRZ1ySWBdD9bru6IR-_WXUgJqvrtx9) *"，*的演讲，他提到为了在围棋中获得 **X** 的特性，我们必须首先展示足够多的*例子*来说明为什么**Y** 是一个问题，这样才能引起围棋团队的注意，帮助他们理解我们的 我们需要让他们相信 Y**Y**是一个真实的问题，X 可能是最好的解决方案。

![](img/35415ccacd5e3f43cb7d13aa55d78500.png)

So . . . now what??

为了实现这一点，我们需要创建所谓的[体验报告](https://github.com/golang/go/wiki/ExperienceReports)，基本上是关于上述内容的帖子。所以我们在这里。

此外，我想指出的是，这篇文章并没有**而不是**涵盖任何具体的实现示例，而是简单地看了一下 [stdlib](https://hackernoon.com/tagged/stdlib) 的一些 pkg 如果实现了类似泛型的东西可能会是什么样子。

# 问题是

我喜欢把这个问题描述为:

在保持类型安全的同时声明动态行为的泛型/方法的缺乏，迫使用户通过以下任一方式来增加他们的代码库:

*   代码生成[工具](https://hackernoon.com/tagged/tools)的使用只会让最终用户更加难以阅读代码，并且由于各种函数的名称相似而不是只有一个，会造成混淆。—为了类型安全而牺牲代码可读性和 API 复杂性—
*   使用**接口{}** *(空接口)*来避免大量名称相似但几乎处处以类型转换/断言为代价的函数。—为了可读性和减少用户 API 而牺牲类型安全—

在这两种情况下，我们使用的代码实际上并不属于应用程序的核心逻辑，而是像胶水一样，几乎每个项目都需要它。

## 一个小例子

我知道你在想什么，*“这不是来自 stdlib……”*等一下，这是来自 [discordgo](https://github.com/bwmarrin/discordgo) pkg，它是上面第二个*案例*的完美例子。如果我们深入研究处理那个**处理程序**变量的代码，我们会遇到类似这样的情况…

不太漂亮，对吧？还有 **37** **更多的情况**到*仅仅是*处理一个**单变量**，而且仅仅是为了记住，这里 pkg 维护者决定公开 1 个函数，并把类型检查的*艰苦工作*留给他们自己，而不是向用户公开 **44 个具有相似名称**的不同函数。所有这些都是为了添加一个简单的处理程序。

# 标准库

在 stdlib 中，你可以找到一些 pkg，当且仅当存在类似于 Go 的泛型时，它们可以极大地减少它们的 API，例如:

## [同步/原子](https://golang.org/pkg/sync/atomic/)

包含 29 个名为*Add****T***，*CompareAndSwap****T***的 pkg 函数，其中 **T** 可以是 *Int32* ， *Int64* ， *Uint32* ， *Uint64* 等等。。。

几乎可以说整个 pkg API 应该是:

刚刚从 29 个名字较大的函数**变成了 5 个名字较短的函数**。

## [同步。地图(Go 1.9+)](https://godoc.org/golang.org/x/sync/syncmap)

是的，这个令人敬畏的并发地图即将到来！到处都有**接口{ T53 }。。。**

在这种情况下，*唯一的*好处是类型安全，因为 API 足够简单。

## [数学/随机](https://golang.org/pkg/math/rand/)

非常类似于*同步/原子*的情况，而不是 *Int，Intn，Int31，Int31n* 呢:

同样的待遇也适用于 [**兰特。兰德**型](https://golang.org/pkg/math/rand/#Rand)型。

## [数学/大](https://golang.org/pkg/math/big/)

也许可以将 **Float** 、 **Int** 和 **Rat** 类型转换为通用 **Number** 类型，并根据泛型计算运算。

其他一些适用的情况有:

*   [**集装箱/清单**](https://golang.org/pkg/container/list/)
*   [**容器/堆**](https://golang.org/pkg/container/heap/)
*   [**容器/环**](https://golang.org/pkg/container/ring/)
*   可能更多，只是还没有做足够的研究

我还认为，比如说，我们有一段非常普通的代码:

我相信如果有泛型，这种操作可以在编译时得到改进/优化，因为编译器*知道*数据的格式是什么样子。下面的例子。

同样适用于所有编码/解码内容，例如 [**编码/xml**](https://golang.org/pkg/encoding/xml/) ， [**编码/csv**](https://golang.org/pkg/encoding/csv/) 不包括在内，因为所有记录总是在**[][][]字符串**中返回，这意味着根本没有反射/类型断言。

我想说的是，在泛型的帮助下，stdlib **可以**在许多地方被缩减和简化，当然第三方 pkg 也是如此。我希望这篇文章至少可以作为一个清晰的例子来说明泛型可以做些什么。