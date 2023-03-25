# 单一责任原则背后的秘密

> 原文：<https://medium.com/hackernoon/the-secret-behind-the-single-responsibility-principle-e2f3692bae25>

## 对编程历史的简短回顾，有助于理解它的基本概念

![](img/09abe68c4f340e73d73b52128c0b21e2.png)

这是第一篇关于[坚实的](https://hackernoon.com/solid-principles-530b2cc2badf)原则以及它们共有的价值观和原则的文章。

## 1972 年，大卫·帕纳斯

早在 1972 年， [David Parnas](https://en.wikipedia.org/wiki/David_Parnas) ，一个封装和[模块化编程](/@wrong.about/how-to-decompose-a-system-into-modules-796bd941f036)概念背后的人，写了他著名的论文 [*“关于将系统分解成模块的标准”*](http://www.cs.umd.edu/class/spring2003/cmsc838p/Design/criteria.pdf) 。在那里，他详细阐述了在决定哪些代码应该放在一起，哪些应该分开时使用的标准。

***帕纳斯的论文到底有什么革命性？它基本上是关于什么的？嗯，是关于两件相关的事情。***

首先，它是关于选择正确的概念来操作。在领域层面上，这些是你的[问题空间](/@wrong.about/on-good-domain-decomposition-385ee8ce5a3)的概念。这些概念在很大程度上相互独立。当你谈论购买一件物品时，你不会考虑它是如何储存的。这些概念的实现也应该相互独立。
在基础设施层面，这些是您做出的主要技术决策。所有使用数据库的代码不应该分散在整个项目中。绝对是单模块。

第二，是封装。当适当分解的领域概念被建模时，它们不应该沉迷于将它们的实现细节散布在整个代码库中。更像是前一点的后果。

这个原则适用于所有层次，而不仅仅是模块层次。它也是关于类和服务的。

***利润***
首先，如果你需要做一些改变，用这种方法更有可能将它限制在一个明确定义和可理解的范围内。你不需要修复一半的项目类。如果你改变了一个商品的购买方式，你就不必修改负责存储它的代码。如果您更改了数据库，就不必修复每个使用数据库功能的地方。

第二，可理解性。每段代码都有明确的目的。

## 1979 年，约登和康斯坦丁

后来在 1979 年，Yordon 和 Constantine 在他们的书 [*“结构化设计:计算机程序和系统设计的基础”*](https://www.amazon.com/Structured-Design-Fundamentals-Discipline-Computer/dp/0138544719) 中创造了[内聚](https://en.wikipedia.org/wiki/Cohesion_(computer_science))这个术语。它就像

> 一个[模块](https://en.wikipedia.org/wiki/Module_(programming))内的元素属于一起的程度

维基百科对此有所阐述:

> 在某种意义上，它是一个类的方法和数据与该类所服务的某种统一目的或概念之间的关系强度的度量。在另一种意义上，它是类的方法和数据本身之间关系强度的度量。

这就引出了第一点:这样的目的应该只有一个。第二点更微妙。这是关于类名与其职责的一致性:这两者的抽象层次应该匹配。下面是我在[软件工程 q & a 站点](https://softwareengineering.stackexchange.com/questions/359741/counterpart-to-single-responsibility-principle-minimizing-number-of-places-to-t/360262#360262)上给出的一个相关问题的例子，说明了第二点的违反:

```
class Car
{
    public function __construct()
    {
    }

    public function drive()
    {
        //
    }

    public function giveFuelToEngine()
    {

    }
}
```

`Car`的责任，它的主要目的——驱动——与方法`giveFuelToEngine.`的抽象层次不相关，因此有不同的改变原因。

## 2003 年，罗伯特马丁

2003 年，Robert Martin 在他的书[*【敏捷软件开发、原则、模式和实践】*](https://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445)中陈述了[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle):

> 一个类应该只有一个改变的理由。

维基百科上说，马丁把这个原则建立在内聚性的基础上，灵感来自于 [*结构化分析和*](https://www.amazon.com/Structured-Analysis-System-Specification-DeMarco/dp/0138543801) 系统规范一书。我对此表示怀疑。汤姆·狄马克采取了以数据为中心的方法，其中数据流是主要关注点。所以这里的内聚性只是一种实现[功能分解](https://en.wikipedia.org/wiki/Structured_analysis#Single_abstraction_mechanism)的手段，尽管如此，这还是很好的。

## 2004 年，埃里克·埃文斯

Eric Evans 在他的书 [" *领域驱动设计:解决软件核心的复杂性* "](https://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215) 中，介绍了一个[集合](https://martinfowler.com/bliki/DDD_Aggregate.html)的概念。它是一组可以被视为一个单元的对象。它有一个突出的特点:聚合只能通过一个[标识符](http://www.informit.com/articles/article.aspx?p=2020371&seqNum=4)来相互引用。请记住，我们不能将任何存储库传递给它，很容易得出结论，一个聚合无法对其边界内的其他聚合进行操作。他们的交流只能通过事件。

这种方法促进了真正内聚的领域概念的创建，除此之外还有明确的边界。这就是内聚性和封装性相遇的地方。

## 包装它

因此，如果一个类是内聚的，如果有一个单一的更高层次的目的，如果它的职责符合它的名字，SRP 就会自然地出现。SRP 只是一个实际的结果，它本身并不是目标。[标识](/@wrong.about/on-good-domain-decomposition-385ee8ce5a3)并赋予其[正确职责](/@wrong.about/how-to-avoid-anemic-domain-model-5e1c3e6fe4d0)的内聚对象是。此外，整个[实体](https://hackernoon.com/solid-principles-530b2cc2badf)只是基本面向对象原则的简化实现结果。只是形式，不是本质。