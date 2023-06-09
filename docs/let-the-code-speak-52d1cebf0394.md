# 让代码说话！

> 原文：<https://medium.com/hackernoon/let-the-code-speak-52d1cebf0394>

![](img/3ce747a6af437f0de591361fbac30edc.png)

你见过这样的代码吗？

我打赌你有(或者你会在你职业生涯的某个时刻看到)。像这样的代码存在于一些遗留系统中，并且通常非常旧。最有可能的是，你看到这样的代码，感觉不是很好。

这段代码的问题在于，它不仅过于冗长，更重要的是，*它隐藏了业务逻辑*(这段代码还有其他一些问题，我们将在本文后面看到)。在企业应用中，**我们写代码解决问题**。因此，我们不应该用代码制造新的问题。请注意，当我们编写“系统代码”或库时，如果我们的目标是高性能，或者我们解决的问题在技术上太复杂，允许牺牲可读性，但即使这样，我们也应该小心谨慎，避免编写隐藏逻辑的晦涩代码。

*Robert c . Martin(Bob 叔叔)*在他的书“[Clean Code:A Handbook of Agile Software crafts](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)”中提到*“阅读(代码)和写作的时间比远远超过 10 比 1”*。在一些遗留系统中，我发现自己花了大部分时间试图理解如何阅读代码，而不是实际阅读代码。测试和调试这样的系统也非常棘手。在大多数情况下，有一种特殊的、不常见的方式与你迄今为止处理的所有事情完全不同。

# 我们写的每样东西都讲述了一个故事

该代码也不例外。代码不应该隐藏用于解决问题的业务逻辑或算法。相反，它应该清楚地指出来。所使用的名称、方法的长度，甚至代码的格式，看起来都应该像是问题已经得到了认真和专业的处理。

你觉得这个代码怎么样？

这段代码看起来像一场战争后的战场。看起来好像每个使用这段代码的开发人员都讨厌这样做，并试图逃离这个地狱，让它处于更糟糕的状态。不同的格式和糟糕的命名清楚地表明不止一个开发人员生活在这个地狱里。听起来像破窗理论，不是吗？不容易说出代码是做什么的(不仅仅是因为看代码的时候眼睛会疼)。此代码片段返回数组的总和减去元素的数量。让我们试着用一种更方便的方式来做:

现在，我们正在使用 Java 8 的流，这使得我们的代码更加简洁易读。

# 干净的代码！

干净的代码*不是让我们的代码看起来漂亮*。干净的代码*是让我们的代码更容易维护*。代码晦涩的时候，大部分时间都花在阅读上。因此，开发人员的生产力降低了。晦涩代码的一个后果是，正如我们前面看到的，使用它的开发人员通常会使它变得更糟。这样做的原因不是因为他们没有能力清理代码，而是因为期限的压力而没有时间。当我们处理晦涩的代码时，很难估计修复一个 bug 或实现一个新特性需要多长时间，因为系统的架构/设计隐藏在代码中。因此，我们最终只是为了完成工作而进行丑陋的黑客攻击，这样增加了技术债务。另一方面，干净的代码表明了作者的意图，因此即使代码中存在 bug，也更容易找到并修复它。从长远来看，干净的代码有助于我们走得更快。我绝对推荐的两本好书是:[Robert c . Martin 的《干净代码:敏捷软件技术手册](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)和 *Martin Fowler* 和 *Kent Beck* 的《重构:改进现有代码的设计》。

对于晦涩代码的可维护性问题，一个解决方案是花几个月(或更长时间)来重构代码并清理它，但是对于企业来说，接受开发在开发人员重构代码时暂停的可能性非常小。那么我们能做什么呢？

# 童子军规则

正如 Bob 叔叔所说，童子军规则背后的思想相当简单:*让代码比你发现的更干净！*无论何时接触旧代码，都要适当清理。不要只是应用一个会使代码更难理解的捷径，而是要小心对待它。该规则更多地关注开发人员应该拥有的心态，这样他们就可以通过使系统更易于维护来使他们的生活从长远来看更容易。

我将诚实地承认，大多数时候处理遗留系统并不容易，尤其是当没有测试或者测试套件不再被维护时，但是我们仍然应该寻找机会使代码更干净。在使用遗留系统时，人们可以采用许多技术(一本很棒的书是由 *Michael Feathers* 写的:“[有效地使用遗留代码](https://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052)”)，但是在这篇文章中，我想集中讨论一些一般性的建议，我发现这些建议对于编写更有表现力的代码很有用。

# 动笔之前先思考

对软件开发有一个误解，认为开发人员(只)写代码。我们没有。**相反，我们用代码**解决问题。代码是媒介，而不是实际的解决方案。按随机键算不算写代码？当然不会，因为这种胡言乱语几乎不可能被计算机解释。这同样适用于没有首先考虑我们试图解决的问题而编写的代码。因此，我们在编写代码时必须非常小心，这样我们通过代码提供的解决方案才是清晰的，而不是模糊的。我们不应该为了写代码而写代码。代码应该解决问题，而不是制造新的问题。

你是否曾经被要求进行代码审查，却发现代码完全错误，唯一的解决方案是从头开始重新编写？我见过许多开发人员，他们一接到任务，就开始在 IDE 中打字。他们认为，如果他们这样做，他们看起来像是在工作。大多数情况下，这被证明是错误的方法，因为不加思考地编写代码会将他们引向错误的方向。当然，一些非常有经验的开发人员可以立即开始编写代码，并且仍然保持正确的方向，但是大多数人需要在实际输入之前进行一些仔细的规划。

考虑下面的例子:

slightly modified example of [https://en.wikipedia.org/wiki/Strategy_pattern#Java](https://en.wikipedia.org/wiki/Strategy_pattern#Java)

这个例子中的代码没有什么不好，对吗？嗯，其实是有的！这里使用了*策略模式*的事实表明这段代码需要有一些灵活性。在这个例子中，与来自维基百科的原始例子不同，我们只有该策略的一个实现，并且没有更多实现的短期计划。这里策略模式的意图可能会误导读者。模式的实现需要一些努力，所以读者自然会想知道做出这个决定的原因是什么。 [Y.A.G.N.I](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it) 原则代表*【你不会需要它】*并且是关于不做不必要的事情。很难预测我们将来需要什么。有时经验会有所帮助，但在大多数情况下，保持简单更安全。

设计模式帮助我们以一种优雅的、易于交流的方式解决特定的问题。如果这样的问题不存在(在前面的例子中不需要可扩展性),代码的读者会被误导，认为问题确实存在。请注意，我并不反对模式。我爱他们！问题是当人们试图发明模式解决的问题时，仅仅是因为他们知道模式。

当我们试图将业务需求的解决方案与模式混合在一起时，同样的问题也会出现。我发现先看问题应该如何以“肮脏”的方式解决要容易得多。只有到那时，我才会检查哪些模式和抽象可能有助于代码更加灵活和可读。无论我是否实践 TDD，我遵循的规则是首先让它工作，然后让它干净(当然，在 TDD 中，这是由 TDD 的 3 个法则驱动的)。

记住！仅仅因为代码工作，并不意味着我们已经完成了我们的工作！实际上，当代码工作时，我们只完成了一半。我们必须研究代码如何将我们的意图传达给读者。

我们的工具集中有很多工具，我们有责任只在适当的时候使用它们。因为每个人都使用框架和库，所以使用它们是没有意义的。我们必须了解它们解决了什么问题，并以不隐藏业务逻辑的方式使用它们。关于如何处理框架和库的一个很棒的帖子是:*Bob 叔叔*的《[让魔法消失](https://8thlight.com/blog/uncle-bob/2015/08/06/let-the-magic-die.html)》。

# 争取表现力！

现在很多编程语言都自带流支持，比如 Java，Kotlin，JavaScript 等。来帮助我们编写有表现力的代码。流已经用*“if”*语句取代了冗长的循环。流帮助我们以一种比命令性方式更具声明性的方式来思考数据转换。迭代一个集合来查找所有小于某个值的元素是没有意义的。简单地说，对数据流应用一个过滤器。

*映射、过滤和还原*几乎适用于所有支持流的语言。所以，每个人都能理解你写的东西，就像每个人看到 for 循环或 if 语句时都能理解一样。关于这个话题的一个很棒的帖子是:*马丁·福勒*的《[收藏管道](https://martinfowler.com/articles/collection-pipeline/)》。

拥有这样一种富有表现力的方式来处理数据是非常强大的。首先，你不用测试这个功能。你注意到第一个例子中的一位误差了吗😃？它也让我们朝着函数式编程的方向发展。函数式编程有太多的好处，不适合写在这篇博文里(如果你有兴趣学习更多关于函数式编程的知识，我推荐这篇文章"[实用函数式编程](https://hackernoon.com/practical-functional-programming-6d7932abc58b)"，当然，还有关于函数式编程的巨著:[哈罗德·艾贝尔森、*杰拉德·让伊·萨斯曼*和*朱莉·苏斯曼*写的《计算机程序的结构和解释](https://www.amazon.com/Structure-Interpretation-Computer-Programs-Engineering/dp/0262510871)》)，但是我将把重点放在它如何帮助提高代码的可读性上。

本文第一个示例中基于流的解决方案如下:

简单干净。很容易理解它的作用。现在，考虑下面的例子:

当您调用该方法时，您是否期望第二个参数会改变？这个方法真的如其所说吗？方法名合适吗？你真的“得到”了什么吗？

现在呢？

在本例中，返回值是一个新列表。没有参数受到影响。我们只是读取参数并产生一个新的结果。现在理解这个方法做什么，怎么用就容易多了。这种方法可以很容易地与其他方法组合。一般来说，组合是流和函数式编程最重要的好处之一。组合允许我们从数据转换、过滤等方面进行思考。编写比命令式方法更具声明性和表达性的代码。我们写的代码表达了**我们想要**做什么，而不是**如何**完成它！这对于代码的可读性来说是一个显著的改进。

将一个问题分解成子问题，解决每个子问题，然后组合这些解决方案来创建初始问题的解决方案要容易得多。另一方面，当主要目标是性能时，命令式风格可能是必不可少的。关于这个问题的一个有趣的故事是著名的[麦克洛伊 vs Knuth 的故事](http://www.leancrew.com/all-this/2011/12/more-shell-less-egg/)。

注意，Java 8 中的`toList()`收集器返回一个可变列表，而在函数式编程中，我们通常使用不可变的数据结构。尽管如此，我们生成新数据并将参数视为只读的事实提高了方法的可读性和行为。虽然有些方法可能会有副作用，但重要的是一个方法要么有副作用(表现为一个*命令*)要么有一个返回值(表现为一个*查询*)，但在可能的情况下不能两者都有。关于这个话题的更多信息可以在[这篇文章](https://martinfowler.com/bliki/CommandQuerySeparation.html)中找到。

编写表达性代码不是一件容易的事情。阿尔伯特·爱因斯坦有一句名言:“如果你不能简单地解释它，你就理解得不够好。”。因此，当我看到代码中的抽象层次是混合的，例如，与 Dao 交互或直接与数据库对话的 UI 类，或者当它们不应该被暴露时，低层次的细节被暴露，我可以说这不仅违反了[*s . o . l . I . d .*](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design))*原则的*单一责任原则*，而且还存在一些关于问题的混淆。在代码中使用注释来解决这个问题并不是解决办法，我们将在以后的文章中看到这一点。我相信一个人写的代码越简单，表达能力越强，他或她就越能理解问题。*

# *拥抱永恒*

*当物体的状态发生变化而我们却没有注意到时，这确实令人困惑。使用一个返回时可能是半构造的对象也是危险的，特别是当我们处理有多个线程的程序时。共享这样的对象真的很难做到正确。另一方面，不可变对象是线程安全的，也是缓存的理想对象，因为它们的状态不会改变。*

*但是为什么人们会选择易变的对象呢？我相信，最有可能的原因是，他们认为他们将获得更好的性能，因为所使用的内存会更少，因为修改是就地执行的。此外，让对象的状态在其生命周期中发生变化是很自然的。这是我们在 OOP 中学到的。这些年来，我们编写的程序中使用的大多数对象都是可变的。*

*如今，一个系统所拥有的内存量比几十年前大了几个数量级。我们面临的真正问题是可扩展性。处理器速度不再像前几年那样提高，但现在我们有了几十个内核的处理器。所以，为了让我们的项目扩大规模，我们需要利用当前的形势。因为我们的程序需要能够在多个内核上运行，所以我们需要以一种安全的方式编写它们。通过使用可变对象，我们必须处理锁定以确保它们状态的一致性。并发不是一个要解决的小问题。如果你对并发感兴趣，那么你绝对应该阅读由 *Brian Goetz* 撰写的“ [Java 并发实践](https://www.amazon.com/Java-Concurrency-Practice-Brian-Goetz/dp/0321349601)”。另一方面，不可变对象由于其本质，对于在多个线程和处理器之间共享来说是固有安全的。此外，不需要同步的事实为创建具有低延迟和高吞吐量的系统提供了机会。因此，不变性是实现可伸缩性的更安全的选择。*

*除了可伸缩性的好处，不变性使我们的代码更加清晰。在上一节的第一个示例中，作为参数传递的集合在方法调用后发生了变化。如果集合是不可变的，这将被禁止。因此，不变性会驱使我们走向更好的解决方案。此外，由于状态是不变的，所以读者不必记住他头脑中的状态变化。读者只需将一个名称与一个值相关联，而不必记住变量的最新值。*

*关于不变性和编程建议的更多信息，通常可以在由 *Joshua Bloch* 所著的《[有效的 Java(第二版)](https://www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683)》一书中找到。另外，你一定要看的一个精彩演讲是“[有丰富吻痕的价值观](https://youtu.be/-6BsiVyC1kM)”。*

> *程序必须是为人们阅读而写的，并且只是附带地为机器执行而写。*
> 
> **―哈罗德·艾贝尔森，计算机程序的结构和解释**

**这篇文章更多的是关于编写更具可读性和表现力的代码的一般性建议。在以后的文章中，我们将讨论生产代码和测试代码中的气味。我们还将看到如何通过查看测试来发现产品代码中可能存在的设计问题。敬请期待！**

# *进一步阅读*

1.  *[干净的代码:敏捷软件技术手册](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)作者*罗伯特·c·马丁**
2.  *[重构:改进现有代码的设计](https://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672)作者*马丁·福勒*和*肯特·贝克**
3.  *[有效地使用遗留代码](https://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052)由 *Michael Feathers* 编写*
4.  *[计算机程序的结构和解释](https://www.amazon.com/Structure-Interpretation-Computer-Programs-Engineering/dp/0262510871)作者*哈罗德·艾贝尔森*、*杰拉德·让伊·萨斯曼*和*朱莉·苏斯曼**
5.  *[实践中的 Java 并发](https://www.amazon.com/Java-Concurrency-Practice-Brian-Goetz/dp/0321349601)作者 *Brian Goetz**
6.  *[有效的 Java(第二版)](https://www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683)作者*约书亚·布洛赫**
7.  *[让魔法消失](https://8thlight.com/blog/uncle-bob/2015/08/06/let-the-magic-die.html)*
8.  *[收集管道](https://martinfowler.com/articles/collection-pipeline/)*
9.  *[实用功能编程](https://hackernoon.com/practical-functional-programming-6d7932abc58b)*
10.  *[壳多，蛋少](http://leancrew.com/all-this/2011/12/more-shell-less-egg/)(麦克洛伊 vs 克努特的故事)*
11.  *[命令查询分离](https://martinfowler.com/bliki/CommandQuerySeparation.html)*
12.  *[有丰富吻痕的值](https://youtu.be/-6BsiVyC1kM)*

**本帖原图片来源为* [*twemoji*](https://github.com/twitter/twemoji/blob/gh-pages/2/svg/1f910.svg)*