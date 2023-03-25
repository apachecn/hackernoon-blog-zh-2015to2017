# 让我们实现开源模型吧！但是…哪个开源？🔊

> 原文：<https://medium.com/hackernoon/lets-implement-the-open-source-model-but-which-open-source-a89c82d1b494>

## 如果有足够的嘴，所有的术语都是可疑的

![](img/be95e20b202319f698d5bad805c370b7.png)

The picture of a Bazaar in Marrakesh, one of the largest cities in Morocco.

Listen to the audio version!

组织决定复制别人的软件开发“模型”是很常见的。人们倾向于看着他们的**不合理的成功**在会议或博客帖子上炫耀，并开始贴上“X 公司模式”的标签。

> 让我们实现 [Spotify 模式](https://www.infoq.com/news/2016/10/no-spotify-model)！
> 
> 让我们实现网飞模式！
> 
> 让我们实现脸书模式！

这里有一个秘密:[没有简单复制就能成功的“模式”。每个组织都有一种文化。这种文化的某些方面允许独特和成功的行为模式出现。这些模式对环境非常敏感:如果对他们有效，并不意味着对你也有效。](/@fagnerbrack/how-to-reject-the-belief-on-the-silver-bullet-1d86b686acbb)

当你试图实现别人的模式，而对**为什么**有效没有任何理解的时候，有一个名字叫做[货物崇拜](https://en.wikipedia.org/wiki/Cargo_cult_programming)。

> 仅仅因为一个模型适用于一个组织，并不意味着它也适用于你的组织。

同样的事情也发生在[开源](https://hackernoon.com/tagged/open-source)上。

许多组织依靠开源软件来完成他们大部分的工作，但是几乎没有一个组织为他们所使用的项目做出贡献。我和一些使用开源软件的人交流过，他们甚至不明白代码从何而来。仿佛它是生活在互联网最黑暗角落某处的高级人工智能的副产品。

尽管一些开源项目是由一个单独的盈利组织创建的，但它们中的许多都不是。举个例子 [curl](https://curl.haxx.se/) ， [Linux](https://www.linux.org/) 或者 [jQuery](http://jquery.com/) 。那些项目依赖于许多没有报酬的人。然而，软件是构建出来的，有时质量和稳定性比公司的同类产品更好。

不是由组织创建的软件怎么可能成功？

看起来**不合理**，所以……

> 让我们实现开源模型吧！

然而，如果你问三个不同的人“开源”是什么意思，你很可能会得到三种不同的回答。

这个术语变得非常多了。

“开源”正在成为“Github”。

Github 让每个人都可以访问 Git 版本控制系统。您可以创建、编辑和删除文件和文件夹，而无需键入一个`git`命令。它专注于为 Git 存储库添加一个社交和用户友好的方面，这并不是一件坏事。

Github 存储库在线位于它们所属的用户名或组织的名称空间下。Fork 是其他人的名称空间下的原始存储库的副本，它直接引用原始存储库。协作是通过问题和[拉请求](/@fagnerbrack/one-pull-request-one-concern-e84a27dfe9f1)来完成的。

在 Github 出现之前， [Sourceforge](https://sourceforge.net/) 是最受欢迎的公开代码网站。与 Github 今天的注册用户(2000 万)相比，它只有一小部分注册用户(208 万)。

如果想了解更多 2017 年 Github 开源的状态， [Nadia Eghbal 可以比我更好的解释](https://youtu.be/VS6IpvTWwkQ)。

> “开源”这个术语实质上已经变得和“Github”一样了。

在 Github 之前，开源是非常不同的。Linux 是一个榜样，人们刚刚开始公开创建大型软件项目。

没人理解开源。

微软甚至试图让公众相信 Mozilla 的“开源”不是“美国的”。

A documentary called [The Mozilla Story: Making the World You Want](https://youtu.be/yyyoz3yGqrw). Mozilla Chairwoman Mitchell Baker and Greylock’s (and former Mozilla CEO) John Lilly tell the story of the rise of Mozilla. The video is cut in the part when they talk about the day Microsoft tried to convince the public that Open Source was not “American”.

微软的政治企图失败了，Mozilla Firefox 超过了 Internet Explorer，成为当时使用最多的浏览器。

1999 年，[埃里克·s·雷蒙德](http://www.catb.org/esr/)创作了一篇名为[大教堂与集市](https://web.archive.org/web/20030424100429/http://www.catb.org/~esr/writings/cathedral-bazaar/cathedral-bazaar/)的散文。

文章指出，当代码被分发、向公众开放并由许多人贡献时，项目实现了“集市”模型。相比之下，当开发是集中的，不对公众开放，并且只由选定的少数人贡献时，项目就实现了“大教堂”模式。

大多数软件开发项目是作为一个“大教堂”来管理的，这在今天仍然适用。根据 Eric Raymond 的观察，Linux 之所以成功，是因为他们看到了对不同事物的需求。他们做了与当时其他人做的软件开发相反的事情。

Linux 被当作“集市”来管理。

> Linux 之所以成功，是因为它是作为“集市”而不是“大教堂”来管理的。

Eric Raymond 可能没有意识到的是，他的观察还见证了一条原则的应用，这条原则后来在 2001 年被添加到了敏捷宣言中:“响应变化而不是遵循计划”，这条原则也可以翻译成持续集成和持续交付的 T2:

> [……]提前发布。经常释放。倾听顾客的心声。[…]
> 
> ——[Eric Raymond 网站](https://web.archive.org/web/20030424110202/http://catb.org:80/~esr/writings/cathedral-bazaar/cathedral-bazaar/ar01s04.html)上“大教堂和集市”文章中的“尽早发布，经常发布”一章。

与 Github 相反，Linux 使用(现在仍然使用)邮件列表作为主要的协作方式。原因可以在这篇文章中总结[，我引用如下:](https://lwn.net/Articles/702177/)

> [……]内核开发人员仍然使用电子邮件，因为它比任何替代方式都快。

事实上， [Github 不能托管开源 Linux 内核社区](http://blog.ffwll.ch/2017/08/github-why-cant-host-the-kernel.html)，至少有两个原因:

1.  Github 对任何事情都使用 Pull Request，包括个人贡献，而 Linux 内核使用 Pull Request(通过邮件列表)将变更转发给整个**子系统**，或者跨不同的**子项目**同步代码重构或类似的横切变更。
2.  Linux 内核是一棵有多个存储库的单树。它不能使用 Github 模型进行扩展，因为 Github 不支持同时提交给多个**存储库**的 Pull 请求，在它们之间共享一个单独的讨论流。Github 的讨论只发生在单个 Pull 请求中。

![](img/8dfffb419dab9c5cb4f5077a46ab773b.png)

A diagram of the Linux Kernel software development tree of repositories, taken from the slides of Greg Kroah-Hartman’s presentation: “[Patches carved into stone tablets](https://kernel-recipes.org/en/2016/talks/patches-carved-into-stone-tablets/)”. In the diagram, the developers sit at the bottom of the tree, each of them with their own Kernel repository clone. Patches are sent to the driver/file maintainer upstream. The driver/file maintainer sends a Pull Request to the subsystem maintainers on a third level. The subsystem maintainer sends a Pull Request to the Linus Torvalds repository or to the “linux-next” tree.

莱纳斯·托沃兹在树的顶端。他就是埃里克·雷蒙德所说的“仁慈的独裁者”:

> [..当创始人吸引贡献者时，仁慈的独裁者组织从所有者-维护者组织演变而来[…]
> 
> ——大教堂和集市:一个偶然的革命者对 Linux 和开源的思考，[第 101 页](https://books.google.com.au/books?id=F6qgFtLwpJgC&lpg=PA101&ots=k5gzNKvOU8&dq=the%20cathedral%20and%20the%20bazaar%20the%20benevolent%20dictator&hl=pt-BR&pg=PA101#v=onepage&q=the%20cathedral%20and%20the%20bazaar%20the%20benevolent%20dictator&f=false)

Linux 开源模式也在很大程度上基于精英管理。如果你写得很好，并且你的补丁解决了一个重要的问题或者增加了一个重要的特性，那么社区中的某些人会接受它，即使它没有找到通向主线内核的路。

Git 是分布式的。Github 不是。

> Linux 使用 Git 支持的分布式开发模型，与 Github 不兼容。

[开源倡议成立于 1998 年](https://opensource.org/history)。他们创造了“开源”这个术语，并创造了所谓的[开源定义](https://opensource.org/docs/osd)。它指出:

*   非混淆源代码应该是公开可用的。
*   许可证不应要求支付版税或其他费用。
*   许可证必须允许他人修改和衍生作品。
*   该软件不得被任何其他东西阻止，如保密协议。
*   该项目不应歧视个人、团体或努力领域。
*   该软件不应被限制为特定分发的一部分。
*   许可证不得限制与开源软件一起发布的其他软件。

> [……]只有在 OSI 批准的开放源码许可下获得许可的软件才应被标记为“开放源码”软件。
> 
> — [开源倡议网站](https://opensource.org/faq#osd)。

更多信息，请参见注释版的[开源定义](https://opensource.org/osd-annotated)。

> 根据开放源码倡议，对于被称为“开放源码”的东西，它需要符合他们的定义。

许多组织倾向于使用 Github，创建名称空间并使用私有存储库。但是，如果你的代码不开放，不允许被复制和分发，那就违反了开源的定义。所以，离“开源”很远。

许多组织都有负责维护项目子集的团队，每个团队都有自己的存储库。然而，一个开源项目倾向于开发一个单独的**仁慈的独裁者**，他负责照顾项目最可信的版本。

许多组织开始使用 Github，因为可以将他们的存储库存储在一个共享的集中式环境中。然而，开源项目本质上是分布式的，分布式有助于在社区中共享源代码知识，并减少因**仁慈的独裁者**的存在而增加的[总线因素](https://en.wikipedia.org/wiki/Bus_factor)。

许多组织使用 [Github Pull Requests](/@fagnerbrack/one-pull-request-one-concern-e84a27dfe9f1) 作为个人、 [pair](/@fagnerbrack/pair-programming-8cfbf2dc4d00) 或 [mob](https://hackernoon.com/how-mob-programming-will-make-you-more-effective-590a1b7e0418) 贡献给他们的库的常用方式。然而，Git Pull 请求(不要与 [Github Pull 请求](/@fagnerbrack/one-pull-request-one-concern-e84a27dfe9f1)混淆)是为了支持分布式协作环境而构建的。邮件列表用于创建跨越许多存储库所有者的讨论流。

几乎在每个组织中，项目经理都需要向业务涉众报告进展。然而，在开源中，使用软件的开发者才是利益相关者。他们也有专业的技术知识来理解它是如何工作的，以及什么对它更好。

在许多组织中，软件开发角色从初级到高级是分开的，因为需要证明不同的工资范围和期望。在开源中，开发者不需要这样的歧视。他们不打算拿工资去工作。他们这样做是出于热情。他们想要的是影响力，而不是收入。

> 组织倾向于复制开源是如何工作的，但是他们不知道开源是否适合他们。

尽管有时不太清楚“开源”对不同的人意味着什么，但它有一个清晰的、有文档记录的定义。

开源倡议通过分发软件的许可证来定义开源。有些东西不应该被称为“开源”，除非它符合他们的定义。

在 Github 之前，开源是非常不同的。Linux 是榜样，随着时间的推移，为了管理其复杂的社区，它被迫进行扩展。今天，他们不能有效地使用 Github，因为他们的模型不适合。

Github 催生了具有漂亮 UI 的开源流行概念。它降低了准入门槛，允许任何人毫不费力地公开他们的代码。

要在您的组织中实现“开源模型”，您需要理解它的含义。这样，你就能更好地测试他们的一些想法，看看他们中的哪些能在你的环境中发挥作用。

第一步是看看外面有什么，理解为什么“开源”是这样工作的。

问题是:**哪个开源**？

感谢阅读。如果您有任何反馈，请通过 [Twitter](https://twitter.com/FagnerBrack) 、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 联系我。