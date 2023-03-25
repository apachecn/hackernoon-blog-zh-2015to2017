# “有效的 Java”可能如何影响 Kotlin 的设计—第 1 部分

> 原文：<https://medium.com/hackernoon/how-effective-java-may-have-influenced-the-design-of-kotlin-part-1-45fd64c2f974>

![](img/1852d8fcbd2fbeb13f1aef8edb42cb41.png)

Java 是一种很棒的编程语言，但是有一些已知的缺陷、常见的陷阱和从早期继承的不太好的元素(1.0 于 1995 年发布)。约书亚·布洛赫的“[有效的 Java](https://www.amazon.com/gp/product/0321356683/ref=as_li_tl?ie=UTF8&tag=lukle-20&camp=1789&creative=9325&linkCode=as2&creativeASIN=0321356683&linkId=92d90357e585584c81b56f9e2a75a3e1) ”是一本关于如何编写好的 Java 代码、避免常见的编码错误并处理其弱点的备受推崇的书它包含 78 个被称为“条目”的部分，为读者提供关于语言不同方面的有价值的建议。

现代编程语言的创造者有一个很大的优势，因为他们能够分析现有语言的弱点，并自己做得更好。Jetbrains ，一家已经开发了几个非常受欢迎的 ide 的公司，在 2010 年决定为他们自己的开发创造一种编程语言 [Kotlin](http://www.kotlinlang.org) 。它的目标是更简洁和更有表现力，同时消除 Java 的一些弱点。他们的 ide 之前的所有代码都是用 Java 编写的，所以他们需要一种与 Java 高度互操作的语言，并且可以编译成 Java 字节码。他们还想让 Java 开发人员很容易地进入 Kotlin。通过 Kotlin，Jetbrains 想要构建一个更好的 Java。

![](img/78f14d87f776ca2dcf187e9d5cde117e.png)

在重读《有效的 Java》时，我发现很多这些东西对 Kotlin 来说并不是必需的，所以这个关于这本书的内容如何影响 Kotlin 的设计的博客系列的想法就诞生了。

# 1.Kotlin 的默认值不需要更多的构建器

当 Java 中的构造函数有很多可选参数时，代码会变得冗长、难以阅读并且容易出错。为了有所帮助，有效 Java 的第 2 项描述了如何有效地使用[构建器模式](https://en.wikipedia.org/wiki/Builder_pattern)。构建这样一个对象需要大量代码，就像下面代码示例中的 nutrition facts 对象一样。它有两个必需参数(`servingSize`、`servings`)和四个可选参数(`calories`、`fat`、`sodium`、`carbohydrates`):

用 Java 实例化一个对象看起来像这样:

使用 Kotlin，您根本不需要使用构建器模式，因为有默认参数的特性，它允许您为每个可选的构造函数参数定义默认值:

在 Kotlin 中创建一个对象如下所示:

为了提高可读性，您还可以将所需的参数命名为`servingSize`和`servings`:

像 Java 一样，这里创建的对象是不可变的。

我们将所需的代码行从 Java 中的 47 行减少到了 Kotlin 中的 7 行，从而极大地提高了生产率。

![](img/6c48fef24177590b6a6b9cdb778c6fa4.png)

NOT BAD

提示:如果你想在 Java 中创建`KotlinNutrition`对象，你当然可以这样做，但是你必须为每个可选参数指定一个值。幸运的是，如果添加了`JvmOverloads`注释，就会生成多个构造函数。注意，如果你想使用注释，我们也需要关键字`constructor`:

# 2.轻松创建单件

“有效的 Java”的第 3 项展示了如何设计一个 Java 对象，使它表现得像一个单例，这是一个只能实例化一个实例的对象。下面的代码片段展示了一个“单一 Elvis”的宇宙，其中只能存在一个 Elvis:

Kotlin 有[对象声明](https://kotlinlang.org/docs/reference/object-declarations.html#object-declarations)的概念，它为我们提供了开箱即用的单例行为:

不再需要手动构建您的单件！

# 3.现成的 equals()和 hashCode()

起源于函数式编程并简化代码的良好编程实践是主要使用不可变的值对象。第 15 条建议“类应该是不可变的，除非有很好的理由让它们可变。”在 Java 中创建不可变值对象非常繁琐，因为对于每个对象，您都必须自己覆盖`equals()`和`hashCode()`函数。约书亚·布洛赫用了 18 页来描述如何遵守第 8 项和第 9 项中这两种方法的明确的一般契约。例如，如果你覆盖了`equals()`，你必须确保[契约](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#equals%28java.lang.Object%29)的反身性、对称性、传递性、一致性和非无效性都得到满足。听起来更像数学而不是编程。

在 Kotlin 中，您可以简单地使用[数据类](https://kotlinlang.org/docs/reference/data-classes.html)来代替，编译器会自动派生出`equals()`、`hashCode()`等方法。这是可能的，因为标准功能可以从对象的属性中机械地派生出来。只需在你的班级前面输入关键字`data`，就大功告成了。这里不需要 18 页的描述。

提示:最近，Java 的 [AutoValue](https://github.com/google/auto/tree/master/value) 变得流行起来。这个库为 Java 1.6+生成不可变的值类。

# 4.属性而不是字段

第 14 项建议在公共类中使用访问器方法，而不是公共字段。如果您不坚持这个建议，您可能会遇到麻烦，因为字段是直接可访问的，您将失去封装和灵活性的所有好处。这意味着在将来，如果不改变它的公共 API，你将不能改变你的类的内部表示。例如，以后不能限制字段的值，如人的年龄。这就是为什么我们总是在 Java 中创建那些冗长的默认 getters 和 setters 的原因之一。

Kotin 实施了这个最佳实践，因为它使用了带有自动生成的默认 getters 和 setters 的[属性](https://kotlinlang.org/docs/reference/properties.html),而不是字段。

从语法上来说，你可以用`person.name`或`person.age`来访问这些属性，比如 Java 中的公共字段。您可以在以后添加自定义的 getters 和 setters，而无需更改该类的 API:

长话短说:利用 Kotlin 的属性，我们可以获得更简洁的类，并且开箱即用，灵活性更高。

# 5.作为强制关键字替代可选注释

注释是在 1.5 版中添加到 Java 中的。最重要的一个是`Override`，它标志着一个方法正在覆盖一个超类的方法。根据第 36 项，应该经常使用这个可选注释来避免恶意的错误。当你认为你正在从一个超类中重写一个方法，但实际上你没有，编译器就会抛出一个错误。只要你不忘记使用`Override`注释，这就可以工作。

在 Kotlin 中，`override`不是可选注释，而是强制关键字。所以这些讨厌的虫子不会再出现了。编译器会提前提醒你。科特林坚持把这些事情明确化。

# 好了

这是“[高效 Java](https://www.amazon.com/gp/product/0321356683/ref=as_li_tl?ie=UTF8&tag=lukle-20&camp=1789&creative=9325&linkCode=as2&creativeASIN=0321356683&linkId=92d90357e585584c81b56f9e2a75a3e1) ”如何影响 Kotlin 设计的第一部分。您可以[在 twitter 上关注我](https://twitter.com/LukasLechnerDev)从我这里获取新的更新。

下次见！

编辑: [Part 2](/@lukleDev/how-effective-java-may-have-influenced-the-design-of-kotlin-part-2-89844d62ddf3#.o41z4tc0a) 这里的[就是](/@lukleDev/how-effective-java-may-have-influenced-the-design-of-kotlin-part-2-89844d62ddf3#.o41z4tc0a)！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)