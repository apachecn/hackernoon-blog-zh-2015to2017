# 使用 TypeScript 泛型构建一个简单但强类型的缓存实用工具

> 原文：<https://medium.com/hackernoon/use-typescript-generics-to-build-a-simple-yet-strong-typed-cache-utility-9f3c11d0399d>

我们经常构建缓存来提高最终用户的实际和感知应用性能。本文描述了 TS 语言的一个特性——泛型——如何帮助我们构建缓存。

构建缓存(以及任何类型的通用服务实用程序)时，您需要做的第一件事是定义它的公共接口。在其核心，一个典型的缓存有一些方法来存储和检索值。一个更全功能的缓存也可以让你检查缓存，从缓存中清除一个或多个项目，但是为了这篇文章，我们说我们只有两件事要做: **Put** 和 **Get** 。

我们来考虑一下看跌期权。当我们将一个项目放入缓存时，我们通常希望为该项目分配一个生存期。这是物品有效的时间窗口 ***** 。我们最终将从缓存中检索项目，为了做到这一点，我们需要某种方法来唯一地标识缓存的项目。我们称这个唯一的身份为物品的**键**。把这些放在一起，我们就有了一个可行的 Put 函数签名:

> public Put(key: string，data: any，lifetimeInMilliseconds:number):void

有了**得到**，事情就有趣多了。正如您所看到的，当我们将数据放入缓存时，它是“任何”时间。这很好，因为缓存与其缓存的数据类型无关(至少在理论上如此)。如果我们不进一步，我们会得到这样的 Get 签名:

> public Get(key: string): any

使用泛型我们可以在 TypeScript 中做得更好。我们可以用一个泛型类型来修饰 Get()，并告诉它我们期望检索的实际数据类型。看起来是这样的:

> public Get <t>(key:string) : T</t>

这是在告诉 TypeScript 编译器，我们将针对缓存发出一个 **Get** 。然而，我们也要告诉它，我们已经知道/期望响应的数据类型:“T”。

想象一下，如果我们缓存了两个对象，一个名为“UserProfile ”,另一个名为“FavoriteItem”。对于 [TypeScript](https://hackernoon.com/tagged/typescript) 泛型，我这样做:

> const cached profile = cache service。get<userprofile>(“some key”)；
> const cached aves = cacheService。get<favorite item[]>(“someOhterKey”)；</userprofile>

因为我们现在告诉 Get 我们在第一种情况下期望一个 UserProfile，在另一种情况下期望一个 FavoriteItem 数组，所以相应的 const vars 现在是强类型的。

这是一个非常基本的例子，泛型可以走得更远。例如，一般来说，考虑 JS 对象实际上是复杂的东西。在我编写的真实缓存实用程序中，我的可缓存对象很简单——它们只保存数据。它们没有自己的函数或包含其他嵌套对象。因此，我可以使用 JSON.stringify()和 JSON.parse()安全地序列化和反序列化它们。这对于更复杂的对象来说还不够好。使用泛型，我们可以告诉 TypeScript，我们只允许使用实现智能 Serialize()和 Deserialize()方法的<t>。代码应该是这样的:</t>

> 公共 Put <t extends="" serializable="">(key: string，data: T，lifetime:number)；
> public Get < T 扩展 Serializable>(key:string):T；</t>

在运行时，TS 编译器不让我们在对象上使用这种特殊的缓存服务，除非它们实现了一个名为“Serializable”的接口。

如果你想了解更多这方面的内容，可以看看我之前写的这篇文章:[https://medium . com/@ pagal vin/implement-binary-search-in-typescript-using-generics-with-used-refactorings-a 4 bcda 932d 7 # . TV 0 of 7 jdo](/@pagalvin/implement-binary-search-in-typescript-using-generics-with-useful-refactorings-a4bcda932d7#.tv0of7jdo)。

泛型给了我们一个很好的工具[来告诉 TypeScript 编译器我们期望数据在运行时是什么样子。这反过来又给了它所需要的信息，以便提供强大的智能感知和其他开发时的好处来帮助我们提高工作效率。](https://hackernoon.com/tagged/tool)

(*)利用时间活着只是众多选择之一。这句话很贴切:

> *计算机科学只有两个硬东西:缓存失效和事物命名。*
> 
> *—菲尔·卡尔顿*

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！