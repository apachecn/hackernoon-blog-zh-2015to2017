# Java 程序员开始使用 C++的 7 个快速技巧

> 原文：<https://medium.com/hackernoon/7-quick-tips-for-java-programmers-starting-c-67103c8f3bd1>

![](img/f122f77ebe199c1d2bad15c3a7251b9e.png)

Photo by [fireskystudios.com](https://unsplash.com/photos/tNALoIZhqVM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 1.避免使用新关键字

在 Java 中，对象是使用`new`关键字实例化的，因此很自然地认为我们应该以同样的方式在 C++中使用`new`:

Java

C++

然而，与 Java 不同，用`new`实例化的 C++变量是*非托管的*，这意味着你必须记住在使用后释放内存。

C++

这可以用`delete`关键字来完成。从 Java 世界来看，这看起来最不方便，最糟糕的是非常容易出错！为什么 C++版本需要额外的语句，而 Java 版本是如此简洁？

在 C++中，有多种方法可以实例化一个对象，首选的方法是直接在堆栈上。

C++

当变量被分配到堆栈上时，一旦它们超出范围，就会被自动删除。这非常简洁，并且具有很大的性能特征。

# 2.C++的`const`比 Java 的`final`更强大

设计大型应用程序时的一个常见模式是使值不可变。这通过减少应用程序的活动部件的数量来降低复杂性。

在 Java 中，我们可以使用`final`关键字将*引用*标记为不可变的。但是，指向的数据可能会发生变化。例如:

Java

因此，为了创建真正不可变的类型，Java 开发人员必须将类中的所有字段标记为`final`:

Java

Java

在 C++中，我们有`const`关键字，它要强大得多。如果一个实例被标记为`const`，那么它的任何成员都不能改变，即使它们在类定义中没有被标记为`const`！

C++

C++

# 3.当心栈切片！

在 Java 中，所有对象的语义都是它们在堆上(由于 JVM 所做的优化，现实要复杂一些，但这是一个很好的基本理解)。

C++非常不同，因为它允许用户决定对象应该放在堆上还是堆栈上。一般来说，我们应该更喜欢堆栈。它给出了可预测的(和快速的！)性能，但是它有一个很大的局限性，因为每个变量的大小在编译时必须是已知的。这是使用继承时的一个大问题。

C++

在这个例子中，我们有两个类`A`和`B`，其中`B`是`A`的子类。每个都有一个返回类名的函数`what`。

给定这些定义，你认为下面会做什么？

C++

如果您来自 Java 世界，答案可能会让您大吃一惊！原来当`y`被强制转换为类型`A`并赋值给`z`时，它采用了类`A`的`what`方法。

在 C++中，当一个堆栈变量被赋给一个超类时，它会获取该超类的成员和方法实现。这是因为变量值的大小不能超过其类型的大小。对于栈变量，编译器只是“切片”掉子类的额外信息。

为了防止这种情况，变量可以由*指向*。一个指针(或[引用](https://en.wikipedia.org/wiki/Reference_(C%2B%2B)) [)](https://en.wikipedia.org/wiki/Reference_(C%2B%2B)))) 总是有相同的大小，不管被指向的值的大小。

C++

## 快速旁白:value_ptr

我们发布了一个名为`value_ptr`的新智能指针，使得在堆上保存值语义变得更加容易。参见:

[](https://hackernoon.com/value-ptr-the-missing-c-smart-pointer-1f515664153e) [## value_ptr —丢失的 C++智能指针

### TL；速度三角形定位法(dead reckoning)

hackernoon.com](https://hackernoon.com/value-ptr-the-missing-c-smart-pointer-1f515664153e) 

# 4.过载你的操作员！

Java 不允许你定义类似于`int`和`boolean`的自定义值类型和操作符。因此，向量和矩阵的实现可能相当麻烦！

Java

Java

在 C++中，我们可以重载`+`操作符:

C++

这使得矢量可以和`+`一起使用，就像内置的原语一样。

C++

# 5.利用编译时编程

Java 的泛型非常简单，大部分只对集合类型有用。C++模板极大地扩展了编译时编程的可能性，在某些方面更类似于 Java 的注释。

回忆一下我们之前的`Vector2`课。如果我们想用 Java 实现一个三维版本，那么我们需要创建一个新的类:

Java

在 C++中，我们可以将它作为类型的一个参数，并编写只处理一次所有大小的通用代码！代码是在编译时生成的，因此通用代码的效率不亚于手写代码。

C++

有了这个通用定义，我们可以很容易地创建任意长度的向量:

C++

模板甚至可以加快编译速度！请看下面我们的对比:

[](https://hackernoon.com/comparing-the-compilation-times-of-templates-and-macros-d0a1b7264a17) [## 比较 C++模板和宏的编译时间

### TL；速度三角形定位法(dead reckoning)

hackernoon.com](https://hackernoon.com/comparing-the-compilation-times-of-templates-and-macros-d0a1b7264a17) 

# 6.使用 auto！

Java 著名的[缺少一个可变类型推理关键字](http://openjdk.java.net/jeps/286)，比如`auto` (C++)、`var` (C#)、`val` (Kotlin)或者`let` (OCaml)。这可能会使 Java 代码变得非常冗长，特别是当像[SimpleBeanFactoryAwareAspectInstanceFactory](https://docs.spring.io/spring/docs/2.5.x/javadoc-api/org/springframework/aop/config/SimpleBeanFactoryAwareAspectInstanceFactory.html)这样的类处于混乱状态时！

有了 C++，编译器可以为你找出许多类型，节省你一些打字和使代码更可读。

C++

# 7.准备使用更多的库

不管是好是坏，JVM 为您提供了许多现成的平台抽象。相比之下，C++非常精简。它缺乏对文件系统、网络和图形的内置支持。相反，C++开发人员必须利用库来实现这一功能。

一个好的库也会抽象平台差异，给出一组通用的可移植函数，就像 Java 一样。严格来说，这两种方法都不更好。Java 开发人员受益于更加统一的生态系统，因为每个人都在使用相同的底层 API。C++开发人员不再受他们不需要的功能的束缚，但他们也必须对使用什么做出更多的决定，并花费更多的时间来集成它。参见:

[](https://hackernoon.com/approaches-to-c-dependency-management-or-why-we-built-buckaroo-26049d4646e7) [## C++依赖管理的方法，或者我们为什么要建立 Buckaroo

### C++是一种不寻常的语言，因为它还没有一个占主导地位的包管理器(我们正在努力！).结果是…

hackernoon.com](https://hackernoon.com/approaches-to-c-dependency-management-or-why-we-built-buckaroo-26049d4646e7) 

# 牛仔

我们创建了 [Buckaroo](https://github.com/LoopPerfect/buckaroo) 来使集成 C++库变得更容易。如果你想尝试一下，最好从文档开始。您可以在 [Buckaroo.pm](https://buckaroo.pm/) 上浏览现有套餐，或者在[愿望清单](https://github.com/LoopPerfect/buckaroo-wishlist)上请求更多套餐。