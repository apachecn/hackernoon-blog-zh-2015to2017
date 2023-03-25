# Javascript 中面向方面的编程(ES5+\Typescript)

> 原文：<https://medium.com/hackernoon/aspect-oriented-programming-in-javascript-es5-typescript-d751dda576d0>

在过去的 8 年里，世界发展得非常快。但是我们陷入了一些问题，我将在本文中尝试解释这些问题并提供解决方案。

OOP 试图通过将关注点分离到不同的实体中来面对开发过程。解构问题域是组织我们的代码所必须的。同样使用 SRP ( [**单责任原则**](https://en.wikipedia.org/wiki/Single_responsibility_principle) )和 IoC ( [**反转控制**](https://en.wikipedia.org/wiki/Inversion_of_control) )。

[但是有时候 OOP 是不够的](https://en.wikipedia.org/wiki/Object-oriented_programming#Criticism)。通常，我们习惯于复制和粘贴代码块来使事情工作。我希望你同意我说的，这是丑陋的，不容易阅读。

有多少次你试图弄清楚“这个方法到底是做什么的？?"因为基础设施代码分散在各处，隐藏了真正的目的，不是吗？：

上面提到的。该方法(::doCheckout)的目的是将 shippingDetails 分配给实例属性，然后将其发送到服务器。为了实现这一点，现在我们习惯于编写基础设施代码，例如验证方法参数，然后发出 AJAX 请求(*从其他地方复制并粘贴代码，并更改一些变量……*)等等。

如果你每次面对这种情况都要重复这一点，那你就没有尽力。

> 较少的代码比干净的代码更好

当然，这只是一个例子，现实世界的问题并不简单。我们想实现这一点，不是吗？([跳到库](https://github.com/k1r0s/kaop-ts))

# AOP 简介

[面向方面编程](https://en.wikipedia.org/wiki/Aspect-oriented_programming)是关于粉碎 CCC ( [**横切关注点**](https://en.wikipedia.org/wiki/Cross-cutting_concern) )。

不是所有的基础设施代码都是 CCC 的，但有些是。如果我们粘贴相同的代码，每次改变一些变量或参数来适应当前的上下文。该代码是 AOP 的主要候选代码。

该代码可以被抽象并准备好在某个方法之后或之前**被注入**，并且能够接收方法/实例上下文来执行操作，就像它(代码)在这里一样。****

这不是魔术，只是 IoC。

> 简而言之，AOP 是一个反对重复的工具箱

如果你还不能理解这篇文章[想想随处可见的 console.log】，那就哭去吧。](http://blog.mgechev.com/2015/07/29/aspect-oriented-programming-javascript-aop-js/)

# 以 KAOP/KAOP-TS 为特色

我通过尝试在 ES5 中实现 OOP 特性来构建这个库。

例如，允许继承而不弄脏原型和覆盖父方法。在下面的代码中，我声明了**程序员**类扩展**人**类覆盖其构造函数:

现代版本的 Javascript 就是这么做的。但是当我成功时，我意识到“override”只是一个在方法执行之前注入的*代码块，它利用带注释的方法的上下文来做一些事情……***这是一个有趣的技术，它提醒我** [**AOP 联盟**](http://aopalliance.sourceforge.net/) **或者 Java 中的 Spring***。*

basically apply just executes super call with invoked arguments while implement actually injects super method

上面的例子是一个“override”建议的实现，它放在我们用来覆盖的方法之前。

当你要实现一个*建议*(这是一个抽象代码块)**，**时，你习惯于获取方法或实例上下文。 **meta** 关键字是作用域元数据，它包含几个允许[反射](https://en.wikipedia.org/wiki/Reflection_(computer_programming))的属性:

both kaop and its TypeScript version kaop-ts have the same API to access scope context

> AOP 是关于定义和应用模式/行为

与复制和粘贴代码块不同，使用 KAOP，您可以提取代码块，使其更加抽象，然后将其注入到需要的地方。

KAOP 提供了一种构建代码的方式，允许 [**声明式编程**](https://en.wikipedia.org/wiki/Declarative_programming) ，其目标是 **:**

> “根据[](https://en.wikipedia.org/wiki/Problem_domain)**，通过描述 ***程序必须完成什么*来消除副作用，而不是描述*如何作为[编程](https://hackernoon.com/tagged/programming)语言[原语](https://en.wikipedia.org/wiki/Language_primitive)的序列来完成它”。*****

***甚至你也有能力自己构建**依赖注入**。因为 AOP 和 DI 都是 [**反射技术**](https://en.wikipedia.org/wiki/Reflection_(computer_programming)) **:*****

***simplified from [https://github.com/k1r0s/ritley/blob/master/lib/abstract-resource.js](https://github.com/k1r0s/ritley/blob/master/lib/abstract-resource.js)***

***前面的例子包含一个资源类，它采用一个 nodeProvider，如果将它注入到构造函数**中，它将为每个资源子类提供一个单独的节点 http 服务器实例**，所有这些实例都将侦听具有自己的**资源标识符**的请求。所以简单地说，我们负责监听每个实例的请求，代码组织得很好，很容易在 OOP 模式中扩展。***

***如果你喜欢这篇文章，请查看[这个带有 Angular 2+ app 的例子](https://hackernoon.com/angular-tutorial-separation-of-concerns-using-es7-decorators-ed6c9756265)***

***今天到此为止，**如果你关心这个话题**，我强烈推荐[你查看这个](https://k1r0s.github.io/aop-intro/)。***

***欢迎任何投稿:***

***[](https://github.com/k1r0s/kaop) [## k1r0s/kaop

### kaop——高级 OOP 库，带有创建类、继承、提供者、注入器、建议，支持方便的反转…

github.com](https://github.com/k1r0s/kaop) [](https://github.com/k1r0s/kaop-ts) [## k1r0s/kaop-ts

### 简单而强大的 ES2016 Decorators 库，带有像 BeforeMethod 这样的强类型方法拦截器…

github.com](https://github.com/k1r0s/kaop-ts) 

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 T21 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！***