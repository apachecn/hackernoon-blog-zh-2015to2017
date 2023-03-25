# 不可变对象的 5 个优点值得您在下一个项目中考虑

> 原文：<https://medium.com/hackernoon/5-benefits-of-immutable-objects-worth-considering-for-your-next-project-f98e7e85b6ac>

![](img/5148f444c830fff2435afb7d0c492946.png)

当第一次学习面向对象的[编程](https://hackernoon.com/tagged/programming) (OOP)时，你通常会创建一个非常基本的对象并实现 getters 和 setters。从那时起，对象就是这个可塑数据的神奇世界。然而，您会惊奇地发现，有时去掉改变对象中数据的能力会使代码更加简单易懂。不可变对象就是这种情况。

在编程中，不可变对象是一种在创建后其状态不能被修改的对象。虽然乍一看这似乎不是很有用，因为 getters 和 setters 通常是为对象创建的第一个函数，但是不可变对象有许多明显的好处。

# 线程安全

假设根据定义，不可变对象是不可更改的，那么在使用它们时就不会有任何同步问题。无论哪个线程正在访问一个对象的版本，都保证它拥有与原来相同的状态。如果一个线程需要该对象的新版本或修改版本，它必须创建一个新版本，因此任何其他线程仍将拥有原始对象。这导致更简单、更线程安全的代码。

[PHP](https://hackernoon.com/tagged/php) 特别不支持开箱即用的线程，所以当使用这种语言时，这种好处并不明显。

# 没有无效状态

一旦给了你一个不可变的对象并验证了它的状态，你就知道它会一直保持安全。在你不知道的情况下，程序中的其他线程或后台进程都不能改变这个对象。此外，应该提供拥有一个完整对象所需的所有数据。这对于需要高安全性的 it 程序非常有用。例如，如果给一个对象一个可以写入的文件名，你知道没有什么可以改变它在你身上的位置。

例如，下面的代码概述了构造一个不可变对象的样子。由于`SimplePerson`类需要它的所有数据在构造函数中，我们可以确定拥有一个有效对象的所有必要数据将总是存在。相反，如果我们为这个数据使用 setters，就不能保证在我们收到对象的时候已经调用了像`setAge($age)`这样的函数。

```
<?phpclass SimplePerson
{
    public function __construct($name, $age) { ... }
}
$universe->create(new SimplePerson(‘Joe’, 42));
```

# 更好的封装

当在代码库中传递不可变对象时，可以更好地封装方法。因为您知道对象不会改变，所以每当您将该对象传递给另一个方法时，您可以确信这样做不会改变调用代码中该对象的原始状态。

这也意味着这些对象总是可以通过引用来传递，不需要担心像[防御性复制](http://en.wikipedia.org/wiki/Defensive_copy)这样的解决方案。

此外，当出现任何问题时，调试将更容易。你可以确定对象被给你的状态没有改变，因此更容易找到错误的源头。

下面的代码有助于概述这一优势。在这段代码中，我们创建的两家汽车修理厂都有一辆与之相关联的奔驰汽车，因为我们的对象不是不可变的。这不是预期的结果，这只是使用可变对象的一个不好的副作用。

```
<?phpclass AutoBodyShop {
   public function __construct($car) { ... }
}class Car {
   public function setMake($value) { ... }
}$car = new Car();
$car->setMake(‘BMW’);
$autoShopOne = new AutoBodyShop($car);
$car->setMake(‘Mercedes’);
$autoShopTwo = new AutoBodyShop($car);
```

# 测试更简单

与更好的封装齐头并进的是更易于测试的代码。可测试代码的好处是显而易见的，它会导致更健壮和无错误的代码库。当你的代码以一种副作用更少的方式设计时，就有更少的令人困惑的代码路径需要追踪。

# 更具可读性和可维护性的代码

任何针对不可变对象的代码都不需要考虑影响使用该对象的代码的其他区域。这意味着代码中混合和移动的部分更少。因此，你的代码会更干净，更容易理解。

总之，利用不变性有明显的好处。如果做得正确，它可以产生更易理解和可测试的代码，这在任何代码库中都是巨大的胜利。根据您使用的语言，这些好处可能会比其他的更大。例如，在多线程程序中，不变性会减少代码中的混乱和副作用。然而，不管使用哪种语言，您肯定会从这个功能中获得一些好处。

*这篇* [*文章*](https://buttercms.com/blog/5-things-you-should-know-about-immutable-objects) *最初发表于* [*ButterCMS 博客*](https://buttercms.com/blog/) *。*[*ButterCMS*](https://buttercms.com)*是一个* [*托管的 API——首先是 CMS*](https://buttercms.com/api-first-cms/) *和* [*博客引擎*](https://buttercms.com/ruby-blog-engine/) *，让你构建基于 CMS 的应用。*