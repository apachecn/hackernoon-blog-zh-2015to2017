# 利斯科夫替代原理

> 原文：<https://medium.com/hackernoon/liskov-substitution-principle-a982551d584a>

## 或者如何创造美丽的缩写

![](img/352387d2703e2375e5b3d6a41f71dcdb.png)

这是第三篇关于[坚实](https://hackernoon.com/solid-principles-530b2cc2badf)原则的文章——如果你错过了，可以看看[的开闭原则](/@wrong.about/the-open-closed-principle-c3dc45419784)。在这里，我首先简要介绍这个概念是如何形成的，并演示一个描述性的例子(没有正方形和长方形，我保证！).

## [贝特朗·迈耶](https://en.wikipedia.org/wiki/Design_by_contract)，1986 年

Bertrand Meyer 首先引入了契约的概念，并用他的语言实现了契约。合同由[前置条件](https://en.wikipedia.org/wiki/Precondition)、[不变量](https://en.wikipedia.org/wiki/Invariant_(computer_science))和[后置条件](https://en.wikipedia.org/wiki/Postcondition)标识。这里有一个来自维基百科的很好的总结:

> 1.[例程可以]期望调用它的任何客户端模块在进入时保证某个条件:例程的前提条件——对客户端来说是一项义务，对提供者(例程本身)来说是一项好处，因为它使它不必处理前提条件之外的情况。
> 2。保证退出时的某个属性:例程的后置条件——对供应商来说是一项义务，对客户来说显然是一项好处(调用例程的主要好处)。
> 3。维护某个属性，在进入时假设，在退出时保证:类不变量。

我只知道在一种语言中契约是一个内置的概念——那就是 Eiffel 本身。在其他语言中，契约可以通过方法的签名来执行:参数的类型、返回值的类型以及可以抛出的异常。但是因为发明一个可以用来执行所有现有业务规则的类型系统几乎是不可行的，所以这种方法非常有限。

## 芭芭拉·里斯科夫，1994 年

芭芭拉·利斯科夫提出她的原则的方式代表了一种[亚类型](https://en.wikipedia.org/wiki/Subtyping)的概念，或子类型多态性。这种类型的多态性在面向对象编程中是最常见的，通常简称为“多态性”。利斯科夫使迈耶的方法正式化，它看起来更像是学术上的:

> 设φ(x)是关于 t 类型的对象 x 的一个可证明的性质，那么φ(y)对于 S 类型的对象 y 应该是真的，其中 S 是 t 的子类型。

[的人类可读版本](https://en.wikipedia.org/wiki/Liskov_substitution_principle#Principle)几乎重复了 Bertrand Meyer 已经说过的所有内容，但是它完全依赖于一个类型系统:

> 1.不能在子类型中加强前提条件。
> 2。子类型中的后置条件不能被削弱。
> 3。超类型的不变量必须保留在子类型中。

## 罗伯特·马丁，1996 年

罗伯特·马丁让这个定义听起来更加流畅简洁:

> 使用基类引用的指针的函数必须能够在不知道的情况下使用派生类的对象。

然而，他没有引入任何新的东西。

## 违反利斯科夫替代原理

我经常发现，为了理解一些原则，意识到它被违反是很重要的。这就是我现在想做的。

违反这一原则意味着什么？这意味着一个对象不履行由接口表达的抽象所强加的契约。或者，换句话说，这意味着你[识别出你的抽象是错误的](/@wrong.about/on-good-domain-decomposition-385ee8ce5a3)。

考虑下面的例子:

```
**interface** Account
{
    */**
     * Withdraw $money amount from this account.
     *
     ** ***@param*** *Money $money
     ** ***@return*** *mixed
     */* **public function** withdraw(Money $money);
}

**class** DefaultAccount **implements** Account
{
    **private $balance**;

    **public function** withdraw(Money $money)
    {
        **if** (!$this->enoughMoney($money)) {
            **return**;
        }

        $this->**balance**->subtract($money);
    }
}
```

这是违反 LSP 的吗？是的，因为帐户的合同规定帐户将被撤销，但情况并非总是如此。那么我该怎么做才能修复呢？我只是修改合同:

```
**interface** Account
{
    */**
     * Withdraw $money amount from this account if its balance is enough.
     * Otherwise do nothing.
     *
     ** ***@param*** *Money $money
     ** ***@return*** *mixed
     */* **public function** withdraw(Money $money);
}
```

瞧，现在合同满意了。

这种微妙的违反通常会使客户有能力区分所使用的具体对象。例如，给定第一个客户的合同，它可能如下所示:

```
**class** Client
{
    **public function** go(Account $account, Money $money)
    {
        **if** ($account **instanceof** DefaultAccount && !$account->hasEnoughMoney($money)) {
            **return**;
        }

        $account->withdraw($money);
    }
}
```

而这自动违反了[开闭原则](/@wrong.about/the-open-closed-principle-c3dc45419784)。

这一点也解决了我经常遇到的关于 LSP 违反的误解。就像“如果父母的行为在孩子身上发生了变化，那么它就违反了 LSP”。不会——只要孩子不违反父母的契约。

## 固体中的利斯科夫替代原理

我对这个缩写的问题是，如果“多态性”存在，为什么“封装性”和“可组合性”不存在？这些都是非常重要的。大概是因为用“e”和“c”很难想出漂亮的缩写吧？我打赌是的。

## 包装它

不要误解我，我喜欢 SOLID 和它所提倡的方法。但这只是其基础中更深层原则的一种形式。上面的例子清楚地表明了这个原则的目的:它是[松耦合](https://en.wikipedia.org/wiki/Loose_coupling)。换句话说，不要让你的客户关心使用的是什么具体的类。崇高的目标，但如何实现呢？首先，从[分解你的问题空间](/@wrong.about/how-to-avoid-anemic-domain-model-5e1c3e6fe4d0)——领域开始。第二，用简单的英语在方法签名中表达你的契约。