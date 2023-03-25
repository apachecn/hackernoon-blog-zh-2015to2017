# 可见性在软件设计中的作用

> 原文：<https://medium.com/hackernoon/affordance-in-software-design-12cc0d9d2721>

## 构建软件类似于制作一个真实的物体

![](img/0a7a00132ae5440e993a4c38c779c1a5.png)

The picture of a gun built with the muzzle pointed towards the shooter. Not very useful I guess...

我非常喜欢战争纪念碑。

有机会近距离观看士兵们在战争中穿戴的真实物品真是太棒了。它们被保存下来，培养了那些从未体验过它们的人的想象力。文物，自从第一次被放进玻璃里就不能碰。

当走过[澳大利亚战争纪念馆](https://en.wikipedia.org/wiki/Australian_War_Memorial)的二战室时，可以看到当时的设备质量是多么的差。想象一下，一个士兵拿着一把没有激光瞄准器或电子仪器的机枪，只是一块为**精心制造的天真的铁，主要目的是伤害一个生物**，制造来触发一个机制，将火药压向弹壳，并将子弹高速扔向指定的方向。

这时你就会意识到工程学是多么令人生畏。

令人难以置信的是，一个工具可以被制造出来，仅仅是为了被一个人使用。

> 工程学是令人生畏的，因为它允许精心制造一种工具，而这种工具的唯一目的就是被人操作。

尽管机关枪是为士兵瞄准和射击而制造的，但它可以有多种用途。

任何人都可以用它通过用铁的硬度击打敌人的头部来击倒敌人。在这种情况下，它可以被不知道如何射击的人使用，但**朝着同样的目的**。

通过利用枪的格式，它可以用来打开一瓶啤酒。在这种情况下，它也可以被一个不知道如何射击的人使用，但**朝着一个不同的目的**这是在创造枪时从未想过的。

据说打开一个瓶子，用熨斗打人，射出一颗子弹是一把二战机枪**能让**一个人做的事情。

一挺机枪提供使用手段的能力被称为**启示**。

用更一般的术语来说:

> 启示是对一个对象或环境进行操作的可能性。
> 
> — [维基百科上的启示](https://en.wikipedia.org/wiki/Affordance)

但是机枪和启示跟[软件开发](https://hackernoon.com/tagged/software-development)有什么关系呢？

启示(或者更确切地说是[感知启示](http://johnnyholland.org/2010/04/perceived-affordances-and-designing-for-task-flow/))也可以是衡量你所编写的代码和你所创建的 API 的有效性的一个标准。一个具有清晰可见启示的 API 允许开发者理解它的目的，并在它被设计的[控制论环境](/@fagnerbrack/the-programming-cybernetic-environment-7202838593a2)中无缝地使用它。

假设你正在使用面向对象的原则创建一个游戏。它有一个使用了`MachineGun`的`Soldier`类:

[https://gist.github.com/FagnerMartinsBrack/0a2e40687eb18b9a64d9034c69636b58](https://gist.github.com/FagnerMartinsBrack/0a2e40687eb18b9a64d9034c69636b58)

你可以设计`Soldier`来使用`MachineGun`对抗敌人。在这种情况下，士兵就是消费`MachineGun` API 的**客户端**。

因为这是[编程](https://hackernoon.com/tagged/programming)而不是现实生活，你可以为将要使用`MachineGun`的客户端定义允许的动作(方法):

[https://gist.github.com/FagnerMartinsBrack/2abc4035b1cbc8f2cfdb5f092ff586ac](https://gist.github.com/FagnerMartinsBrack/2abc4035b1cbc8f2cfdb5f092ff586ac)

然而，仅仅因为一个`MachineGun`拥有`open(bottle)`的功能，并不意味着一个`Soldier`被允许做这件事。这就是为什么之前看到的`Soldier`类没有使用`MachineGun`中的`open(bottle)`方法，而是使用了`useHandEquipmentAgainst(enemy)`方法。

如果环境允许，你可以说**`**MachineGun**`**让士兵射击**。**

**您还可以允许士兵打开一瓶啤酒，但是您需要明确地创建该功能:**

**[https://gist.github.com/FagnerMartinsBrack/f76c1599151d82485cbcbea87f38f77d](https://gist.github.com/FagnerMartinsBrack/f76c1599151d82485cbcbea87f38f77d)**

**以上是一种启示，可以存在于我们创建的这个[控制论环境](/@fagnerbrack/the-programming-cybernetic-environment-7202838593a2)中的相互连接的模块之间，在这个环境中，概念对象都与现实生活相隔离，无论一个对象允许其用户做什么，都可能受到编程语言安全机制的限制——例如，像 Java 静态类型系统或 JavaScript 运行时语法错误。**

**但是，您可以将这种构建可负担系统的方法向前推进一步。**

**如果你把一把未上膛的机枪交给一个石器时代的人，他们可能首先用它来敲碎岩石。这是他们能感知到的该物体的唯一启示。**

**同样的，如果你给一个不知道如何使用它的程序员一个精心制作的编程 API `MachineGun`类，他们可能会在`Soldier`类中创建一个`getWeapon()`来将`Soldier`的武器功能用于其他目的。使用 getter 可能是他们唯一知道的事情，因此也是他们认为代码提供给他们的唯一事情:**

**[https://gist.github.com/FagnerMartinsBrack/36f1cc7d07abb93d82031ad336a79b74](https://gist.github.com/FagnerMartinsBrack/36f1cc7d07abb93d82031ad336a79b74)**

**以上就是违反了一个有据可查的原则叫做[告诉，不要问](https://martinfowler.com/bliki/TellDontAsk.html)和[得墨者法则](http://wiki.c2.com/?LawOfDemeter)。当人们开始在不合适的地方添加 getters 来检索信息时，这是一个非常常见的错误。**

**如果程序员不理解环境的潜在目的和他们正在使用的 API，就很难有效地使用工具提供给他们的功能来实现创建一个高效设计的可持续系统的目标。整个环境允许他们做最初的开发者从未想过的事情——比如添加一个 getter——并且没有什么可以阻止它。**

> **可理解的启示也定义了程序员可以用代码做什么。程序员可以发现新类型的启示，这些启示最初不是为该代码设计的。**

**一个士兵还需要依靠枪的质量来确保它能被有效地使用。比如:射击时，枪要把子弹高速抛向目标，而不是抛向射手。**

**程序员还需要依赖 API 的质量来确保它能被有效地使用。如果`Soldier`有一个方法`equipShootingGun(new HandGun())`和`equipWhiteGun(new Knife())`，调用`.stab()`方法的`Soldier`类的客户端会期望`Soldier`使用`Knife`，而不是`HandGun`来执行动作:**

**[https://gist.github.com/FagnerMartinsBrack/7c5a58cde6056d6675b02b4a164ec200](https://gist.github.com/FagnerMartinsBrack/7c5a58cde6056d6675b02b4a164ec200)**

**上面的例子违反了最小惊讶原则。调用`.stab()`方法时，`Soldier`正在拍摄。从运行`Soldier`实例的客户的角度来看，这是一种惊人的行为。**

**API 的设计者负责显式和隐式地构建 API 的功能。如果他们不在乎这些，一旦系统开始增长，它最终会以一种不同于预期的方式运行。**

> **不要低估程序员找到创造性的方法来消费 API 的能力。**

**API 设计定义了代码允许程序员做什么。如果设计者错误地构建了 API，那么就很难以可预测的方式使用系统的组件。**

**在现实生活中，一把设计糟糕的机枪很容易让士兵朝他们的脚开枪。设计上的失败可能需要很长时间才能被武器使用者注意到。**

**软件也是如此。**

**大多数人只是为了工作而建造它，并不关心设计。只要项目是无害的实验或探索性的编码，那就没什么错。如果你打算构建可伸缩的东西，那么你需要考虑设计，主要是你的 API 和代码提供给消费者去做的事情。**

**有些事情是你无法改变的，比如来自“告诉，不要问”原则或编程语言规则的错误。然而，思考这些概念至少是我们朝着目标前进的一步。**

**现实生活中的工程和软件设计有着相同的基础。**

**对于你消费的东西和你建造的东西，都有可感知的启示，你只需要识别它们并分析它们的权衡。但是请注意，它们可能因人而异。**

**一个 API 也需要 API 设计者的制造技能才能正确运行，对于机关枪的设计者也是一样。**

**所有这些原则可能会帮助你不要造一把最终会打中别人脚的枪。**

**而你，你的代码能让另一个开发者做什么？**

**感谢阅读。如果你有一些反馈，请通过 [Twitter](https://twitter.com/FagnerBrack) 、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 联系我。**