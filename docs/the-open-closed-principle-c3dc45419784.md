# 开闭原则

> 原文：<https://medium.com/hackernoon/the-open-closed-principle-c3dc45419784>

## 隐藏在背后的是什么

![](img/766af9c37c8c0bc4bdba688742573597.png)

这是第二篇关于[坚实](https://hackernoon.com/solid-principles-530b2cc2badf)原则以及它们都有什么共同基础的帖子(此处查看单责任原则[)。](/@wrong.about/the-secret-behind-the-single-responsibility-principle-e2f3692bae25)

## 伯特兰·迈耶，1988 年

这是 Bertrand Meyers 在他的书“ [*面向对象软件构造*](https://www.amazon.com/Object-Oriented-Software-Construction-Book-CD-ROM/dp/0136291554) ”中写的，这个原则来源于此:

> 一个类是封闭的，因为它可能被编译、存储在库中、被基线化和被客户端类使用。但是它也是开放的，因为任何新的类都可以使用它作为父类，增加新的特性。当一个子类被定义时，没有必要改变原来的类或者打扰它的客户。

所以他提议用一个[实现继承](/@wrong.about/inheritance-based-on-internal-structure-is-evil-7474cc8e64dc)。一点都不好。

## 罗伯特·马丁，1996

可怜的罗伯特·马丁经常因为贝特朗·梅耶的提议而受到指责。难怪，看看他在论文中写的[](https://www.cs.duke.edu/courses/fall07/cps108/papers/ocp.pdf)*:*

> *1.它们[模块，以及类]是“开放扩展的”。
> 这意味着模块的行为可以扩展。随着应用程序需求的变化，或者为了满足新应用程序的需求，我们可以使模块以新的和不同的方式运行。*
> 
> *2.它们“关闭以进行修改”。这样一个模块的源代码是不可侵犯的。不允许任何人对它进行源代码更改。*

*我意识到很大一部分读者没有读到更多，并且指责 Robert Martin 使用了实现继承。嗯，只是一个不幸的命名。下面是对他的意思的阐述。他很清楚这一点:*

> *抽象是关键。*

*他演示了一个简单的例子，一个*客户机*类直接依赖于一个具体的*服务器*类:*

```
***class** Client
{
    **public function** doSomeWork()
    {
        **return** (**new** Server())->run();
    }
}*
```

*显然，当*服务器*类必须被替换时，*客户端*会受到影响。所以这个故事的寓意是:引入抽象，让你的客户依赖它们，而不是具体的实现。这都是关于问题空间的正确分解，产生了一些抽象和相当多的可组合、松散耦合和高度一致的实现。*

*在模块层面上，David Parnas 在 1972 年描述的方法最好地应用了这一原则。它加强了[高内聚](/@wrong.about/how-to-decompose-a-system-into-modules-796bd941f036)，而开闭原则建议提取抽象，从而支持松散耦合。*

## *罗伯特·马丁， [2003](https://www.amazon.com/UML-Java%C2%BF-Programmers-Robert-Martin/dp/0131428489/) ， [2004](https://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445) ， [2013](https://8thlight.com/blog/uncle-bob/2013/03/08/AnOpenAndClosedCase.html) ， [2014](https://8thlight.com/blog/uncle-bob/2014/05/12/TheOpenClosedPrinciple.html)*

*在接下来的几年里，他一直在努力阐明他的观点，即不是通过继承，而是通过交换有用抽象的实现来扩展行为。不太成功:2017 年，[有些还是没弄明白](http://www.yegor256.com/2017/03/28/solid.html#o)。*

## *类*参数化**

*我已经列出了两种不用修改现有代码就可以引入新类的方法。第一个是使用继承，这是不鼓励的，第二个是提取新的接口，以便它们可以被注入到一个类中。第三种方式是类参数化，如果我可以这样称呼它的话。它非常接近于引入新的抽象，但它是关于注入数据而不是行为。考虑下面的例子。我有一个计算雇主工资的课程。可以有不同的策略，这反映在下面(为了强调这一点，简化了一个示例):*

```
***class** SalaryService
{
    **const *TAX*** = 0.07;

    **private $salaryStrategy**;

    **public function** __construct(SalaryStrategy $salaryStrategy)
    {
        $this->**salaryStrategy** = $salaryStrategy;
    }

    **public function** calculate()
    {
        **return** $this->**salaryStrategy**->calculate(**self**::***TAX***);
    }
}*
```

*所以当一个新的策略出现时，我不需要修改一个 *SalaryService* 类。这意味着 OCP 在这一要求方面没有受到侵犯。但是，当你被告知一项税收发生了变化时，你需要在代码中反映出来。会不会违反开闭原则？当然，因为你需要修改现有的代码。你有什么选择？简单常量不代表任何行为，它代表数据。尽管您仍然可以通过一个参数注入它，比如说构造函数参数:*

```
***class** SalaryService
{
    **private $salaryStrategy**;
    **private $tax**;

    **public function** __construct(SalaryStrategy $salaryStrategy, $tax)
    {
        $this->**salaryStrategy** = $salaryStrategy;
        $this->**tax** = $tax;
    }

    **public function** calculate()
    {
        **return** $this->**salaryStrategy**->calculate($this->**tax**);
    }
}*
```

## *违反开闭原则*

*在一个新出现的需求的上下文中，上面的代码没有违反 OCP。基本上不存在仅仅是“违反开闭原则”这样的事情。只有在满足某些需求的情况下，才会违反它。所以第一个版本的 *SalaryService* 可能多年来一直遵循开闭原则，直到税收发生变化。*

*每次你需要实现新的需求和修改现有的代码，这都是 OCP 违规。当然，你不可能提前找出你所有的抽象——只是因为你不可能提前找出所有的需求。所以违反开闭原则是可以的——当然，只有在你提取新的抽象时。*

## *总结一下*

*如果作为某个类的一部分的任何行为可以改变，那么它的实现应该隐藏在接口后面。这已经有了一个名字——它就是[松耦合](https://en.wikipedia.org/wiki/Loose_coupling)。但它不能通过提取几个接口来完全机械地实现。这一切都始于[域分解](/@wrong.about/on-good-domain-decomposition-385ee8ce5a3)。*