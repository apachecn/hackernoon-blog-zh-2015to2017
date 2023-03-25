# 论现代软件开发

> 原文：<https://medium.com/hackernoon/on-modern-software-development-13ed8c2c75d8>

![](img/e6e439bf26a2c36e4da7a017e4ed2e11.png)

# 介绍

今天的软件开发与 20 年前有所不同。对于一个软件开发人员来说，要跟上技术的进步，就需要不断地学习。这不是真的。

在这篇文章中，我想说明现代主流软件开发，尤其是 web 应用程序，需要完全不同类型的开发人员。你必须学习并运用一套完全不同的技能。仅仅提高你的核心编码技能是不够的。

现代软件开发是关于了解工具和库的情况以及如何使用它们。知道如何创建一个最佳算法不再是强制性的。

编程已经从 90 年代需要正确的数据结构和算法的复杂应用的传统编码过渡到今天的库、框架的编排。[编程天赋对于阅读和理解外星代码仍然至关重要，但已经不足以成为一名有效的开发人员。](https://hackernoon.com/tagged/programming)

正如“有一个应用程序”，你会发现有一个图书馆的每一个需求。

# 经典软件开发:算法和 Knuth

在过去，事情很清楚。Windows 是操作系统，电脑是桌面。没有智能手机，没有平板电脑，没有云。主流软件开发意味着用像 Visual Studio、Delphi 或 Oracle Forms 这样的 ide 开发桌面应用程序。你买了它们，却不得不接受它们的功能。如果你需要一些缺失的功能，那么你必须自己开发。

你必须依靠你的技能和书架上的书。如果你在一个允许你上网的特权环境中工作，你至少可以从新闻组中得到帮助。即使在那时，你也不得不自己实现许多现在库提供的特性。

我只能推测其中的原因。我可以肯定的一件事是，在互联网的早期，获得软件仍然意味着从你必须首先找到的供应商那里获得软盘或光盘。这意味着从印刷目录或技术杂志上的广告中选择产品。开源刚刚开始，所以你实际上不得不为软件付费。

这是可以理解的，这个耗时的过程让你别无选择，只能自己实现大量的功能。

通过创建自己的库，您向下移动了一个抽象层，并发现自己处于必须确保性能良好、没有内存泄漏并且可以在许多用例中工作的情况下。这需要很好地理解如何实现运行在库之上的公共数据结构和操作——换句话说，就是编写算法。

正式课程符合这些要求。你学习了数据结构和算法。你知道二叉树、链表、各种排序算法的实现，以及使用大 O 符号来计算算法的最佳/最差/平均情况的性能。Knuth 的纲要“计算机编程的艺术”是经典软件开发人员的圣经。

最后但同样重要的是，你必须在数学方面有智慧。

# 现代软件开发:管理和组合库

随着万维网的兴起，主流软件开发从桌面应用转向了 Web 应用。全新的复杂程度影响着项目基础设施、编程语言、数据库设计等等。这种规模意味着单个开发人员，甚至一个团队，都无法拥有使用传统技术完成项目所需的知识。

随着开发者通过互联网向公众传播他们自己开发的库，开源场景得到了极大的推动。因此，图书馆的数量和种类多得令人应接不暇。例如，浏览器库的范围从 Angular 这样的大型框架到 moment.js 或 lodash 这样的小型专业工具。

继续上面的例子，很明显你不应该花时间写时间操作的 JavaScript 实用函数。省下你开发、修复和重构的可怕时间吧！就拿 moment.js 继续前进吧。

除非您必须为一个独特的目标开发一个库，否则您不会比一个在该特定领域具有专业技能的开发人员团队做得更好，他们拥有多年的经验，并受到积极贡献的开源社区的支持。

此外，对于像迭代集合这样的最原始的操作，如果存在库，最好使用它。

例如，最好使用 lodash 之类的库来执行原始操作，如在 Javascript 中执行循环，因为本地 forEach 在某些浏览器中速度较慢。

今天的程序员面临的主要挑战不是制作最合适的算法，而是找到正确的库并知道如何组合它们。在最好的情况下，您只需编写所谓的粘合代码来管理库之间的相互作用。

当然，您的应用程序有一个独特的销售主张，使其与众不同。核心代码应该，而且可能必须，由你的内部团队开发。对于不属于核心的代码，您应该花时间检查现有的库。你应该找到一个能为你提供所有需要的功能并被积极维护的。

# 结论

在导言中，我大胆地声明，跟上技术进步就足以“保持需求”。

通过区分传统和现代软件开发人员，我希望我已经表明了这是另一种类型的工作。从传统开发人员的角度来看，这需要彻底改变思维方式。

尽量避免主动编程，改用被动编程。这意味着阅读他人的代码，并决定它是否适合您当前正在开发的应用程序。如果您的主要编程任务是编写将所有这些库绑定在一起的粘合代码，您应该感到高兴。

这是成为一名优秀开发人员的要素:找到正确的库组合，使它们保持最新，并将自己编写的代码减少到绝对最低限度。理想情况下，您编写的唯一代码应该是使您的应用程序与众不同的部分。

我绝不想诋毁编码。毕竟，您必须理解和评估外部代码。如果你没有良好的编程技能，那是不可能的。

# 相关帖子:

*   [http://blogs . starcio . com/2016/09/modern-software-development-low-code . html](http://blogs.starcio.com/2016/09/modern-software-development-low-code.html)
*   [http://www . infoworld . com/article/2902242/application-development/7-reasons-why-frameworks-is-the-new-programming-languages . html](http://www.infoworld.com/article/2902242/application-development/7-reasons-why-frameworks-are-the-new-programming-languages.html)
*   [https://www . quora . com/Why-should-I-use-a-web-framework-of-pure-code/answer/Matt-Pickering-3](https://www.quora.com/Why-should-I-use-a-web-framework-instead-of-pure-code/answer/Matt-Pickering-3)
*   https://www . quora . com/Is-it-better-to-code-everything-from-scratch-or-use-a-framework-Time-Is-not-a-issue/answer/Mark-Flory
*   [https://jax enter . com/coding-scratch-vs-using-framework-134319 . html](https://jaxenter.com/coding-scratch-vs-using-framework-134319.html)
*   [https://jax enter . com/frameworks-vs-programming-languages-134954 . html](https://jaxenter.com/frameworks-vs-programming-languages-134954.html)
*   [http://www . developer . com/design/article . PHP/3338791/Overcoming-quot not-Invented-here quot-syndrome . htm](http://www.developer.com/design/article.php/3338791/Overcoming-quotNot-Invented-Herequot-Syndrome.htm)
*   [https://medium . com/swlh/from-not-invented-here-to-never-invented-here-1fd 40 e 462553](/swlh/from-not-invented-here-to-never-invented-here-1fd40e462553)

*原载于 2017 年 8 月 20 日*[*www.rainerhahnekamp.com*](http://www.rainerhahnekamp.com/en/modern-software-development/)*。*