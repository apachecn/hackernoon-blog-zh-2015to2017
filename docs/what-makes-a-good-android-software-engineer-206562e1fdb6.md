# 怎样才能成为一名优秀的(Android)软件工程师

> 原文：<https://medium.com/hackernoon/what-makes-a-good-android-software-engineer-206562e1fdb6>

在过去的五年里，我一直在 Android 上构建东西。在此期间，我与来自不同背景和不同经验水平的工程师合作过。一些工程师来自企业背景，拥有多年的经验，而一些工程师刚刚从大学毕业，他们唯一知道的是他们在大学/学院学到的东西以及他们在空闲时间开发移动应用程序的经验。有些人甚至没有接受过正规的计算机科学教育，是自学的。在此期间，我看到了上述类别的每个工程师能够交付什么，以及他们是如何交付的。

所以，如果你正在为你的公司招聘一名所谓的:Android 软件工程师，请阅读这篇文章。为什么？因为，起初，移动应用开发被错误地认为是容易的。毕竟，一个移动应用仅仅是把不同的页面放在一起，然后输出 monolith 后端要求它输出的内容。对吗？这与事实相去甚远。事实上，如果你观察一下 [PlayStore](https://play.google.com/store) 上的应用程序的质量，你会注意到应用程序的质量分布描述了一个[高斯分布](https://en.wikipedia.org/wiki/Normal_distribution):有一些非常差的应用程序，也有一些非常好的应用程序和许多平庸的应用程序。

让我试着在这里定义一下我所说的[平庸](http://www.chentir.com/2016/12/25/do-you-tolerate-mediocrity/)是什么意思:

在最基本的层面上，一个平庸的移动应用程序是一个不能很好地与其运行的环境(平台/操作系统)协作的应用程序。首先，它不符合[平台定义的视觉语言](https://developer.android.com/design/index.html)，因此会使用户困惑。第二，它没有考虑到它是在一个受限的环境(内存、CPU、网络带宽、电池)中运行，因此它破坏了整个设备的用户体验。第三，它只是在某些情况下不起作用(例如网络故障)。这最后一点对于大多数软件来说是全球性的。

简而言之，以上三点总结了构建好的移动应用程序的挑战。最重要的是，该应用程序需要与您公司的基础设施正确集成，并且必须根据大量不断发展的业务领域问题进行编码。

因此，如果我现在不得不雇用一名软件工程师来应对这些挑战，这就是我想要的:

*   体面的软件工程和手工艺技能。这包括像这样的主题:坚实的原则，编写干净的代码，应用程序架构和测试。数据结构和算法也很重要，但是如果一个候选人不知道如何平衡 BST，我不会反对他。我当然关心候选人是否至少知道基本的数据结构(列表、队列、树、散列图……)，理解 Big-O 符号是什么，并且能够谈论时间与内存的权衡。看到了吗？只是基本的东西。
*   精通 Java(尽管如果候选人非常优秀，并且非常精通另一种语言，我不会因此而反对她/他)。
*   对并发的理解。因为应用程序需要快速响应，所以不要在事件线程上执行任何长时间运行的任务是很重要的。这意味着，许多事情需要在单独的线程上执行。如果候选人不知道构建并发应用程序的挑战，他们很可能会在工作中和**生产**中了解到这一点！
*   高度理解分布式系统。随着移动应用程序需要变得越来越响应，它们将天生采用离线优先的方法来设计。这意味着，由于网络可能出现故障，同一用户的应用程序可能处于不同的状态。因此，需要了解[上限定理](https://en.wikipedia.org/wiki/CAP_theorem)和分布式系统中的一致性概念。
*   分析问题和解决问题的能力。候选人是否知道如何思考一个问题，发现所有未知的未知，发现通向解决方案的所有路径，将它们相互比较，提出实施解决方案的计划。
*   能够描述他们当前公司的技术堆栈。重要的是，你雇佣的候选人应该对事物的运作方式有好奇心，并且理解了解全局的重要性。
*   良好的口头和书面表达能力。**在技术上，沟通是** **关键**。至关重要的是，候选人知道如何清楚地传达他们的想法，以及正确记录他们的工作的重要性。此外，他们的写作技能很可能代表了他们编写优秀代码的能力。
*   理解当前的技术背景及其对设计和工程的影响。

最后，我不会担心候选人对 SDK 本身了解多少。只要:候选人擅长上述大多数概念，候选人至少作为用户了解 Android 平台，并在总体上受到移动性的激励，最后候选人是一个快速的学习者。话虽如此，如果你足够幸运地找到一个既有很好的 CS 背景又假装了解 Android 开发生态系统的候选人，确保他们理解一些基本概念。

此类概念包括(并非详尽无遗):

*   能够在板上画图，从高层次上看，Android 架构是什么样子。
*   能够在高层次上描述当你点击 Android Studio 上的 build 按钮时会发生什么。
*   能够在较高层次上描述在设备上安装应用程序时会发生什么。
*   基本了解 Dalvik 和 ART VMs 的工作原理。
*   Android 上进程间通信的工作原理。
*   应用程序如何被沙盒化以及为什么它很重要。
*   权限模型(如何在低级别授予权限)。
*   流程和应用程序生命周期。
*   另一个很好的经验法则是，就候选人擅长的话题向他/她发起强有力的挑战。如果那是 UI，那么就广泛地覆盖它。

正如你所看到的，重点放在验证候选人是否知道核心的基本原理和概念，这表明他们是否能够适应你可能抛给他们的任何一组问题。大声疾呼，不要仅仅因为候选人知道如何使用一两个库就雇佣他们。你不想雇佣图书馆用户，而是那些不仅能在必要时使用他们(当然最好不要重新发明轮子)，而且还能退后一步，分析情况，在问题出现之前预见问题，并针对你的问题集提供独特解决方案的工程师。

> *大声疾呼，不要仅仅因为候选人知道如何使用一两个库就雇佣他们。*

正如[蒙田](https://en.wikipedia.org/wiki/Michel_de_Montaigne)所说:“J'aime mieux une tête bien faite，qu ' une tête bien pleine”([可以翻译为](http://forum.wordreference.com/threads/mieux-vaut-une-t%C3%AAte-bien-faite-plut%C3%B4t-quune-t%C3%AAte-bien-pleine.2321437/):“比起完整的头脑，我更喜欢敏锐的头脑”)。

当然，我在这里描述的候选人是一个相当资深的人。你可以也应该雇佣初级工程师。但在这种情况下，确保你的组织中初级和高级工程师之间保持一个良好的比例。如果你有一个主要由初级工程师组成的团队，你会经历一段混乱的旅程，因为你的团队会在工作中学习很多东西，修复一个又一个关键的产品 bug，一遍又一遍地迭代，直到他们可以获得一个像样的可维护和可测试的代码库等等。

如果你是一名开发 Android 应用程序的软件工程师，并希望改进本文中列出的一些概念，我建议你阅读以下(简短的)阅读清单:

**软件工程与工艺学:**

*   [干净的代码](https://www.amazon.fr/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/ref=sr_1_1?ie=UTF8&qid=1483802012&sr=8-1&keywords=Clean+Code) —鲍伯·马丁大叔
*   务实的程序员——安迪·亨特和迪夫·托马斯
*   [重构](https://www.amazon.fr/Refactoring-Improving-Design-Existing-Code/dp/0201485672/ref=pd_sim_14_2?_encoding=UTF8&psc=1&refRID=K6HSKFNYXSDF1MD8VB98)——马丁·福勒
*   [Head First 设计模式](https://www.amazon.fr/First-Design-Patterns-Elisabeth-Freeman/dp/0596007124/ref=sr_1_2?s=english-books&ie=UTF8&qid=1483802609&sr=1-2&keywords=design+pattern)——伊丽莎白·弗里曼、埃里克·弗里曼、伯特·贝茨和凯西·塞拉
*   [企业应用架构模式](https://www.amazon.fr/Patterns-Enterprise-Application-Architecture-Martin/dp/0321127420/ref=sr_1_1?s=english-books&ie=UTF8&qid=1483802514&sr=1-1&keywords=application+architecture) —作者 Martin Fowler(他的[博客](https://www.martinfowler.com/)也值得关注)

**Java:**

*   [有效的 Java，第二版](https://www.amazon.fr/Effective-Java-Joshua-Bloch/dp/0321356683/ref=sr_1_1?ie=UTF8&qid=1483802142&sr=8-1&keywords=Effective+Java+2) —作者约书亚·布洛赫

**并发:**

*   [实践中的 Java 并发性](https://www.amazon.fr/Java-Concurrency-Practice-Brian-Goetz/dp/0321349601/ref=sr_1_1?ie=UTF8&qid=1483802180&sr=8-1&keywords=concurrency+in+practice)——Brian Goetz

**数据结构和算法:**

*   《算法导论》(又名《科尔曼书》)——查尔斯·e·莱瑟森，克利福德·斯坦，罗纳德·里维斯特和托马斯·h·科尔曼。
    或[算法设计手册](https://www.amazon.fr/Algorithm-Design-Manual-Steven-Skiena/dp/1849967202/ref=pd_sim_14_4?_encoding=UTF8&psc=1&refRID=MQHE83MFHP4JC8GPN140) —作者史蒂文·斯基恩纳

**CS 文化:**

*   订阅 [ACM 队列](http://queue.acm.org/app/)
*   [喜欢报纸](http://paperswelove.org/)
*   收听[软件工程广播](http://www.se-radio.net/)和/或[软件工程日报](https://softwareengineeringdaily.com/)

**解题技巧:**

*   如何解决这个问题 G. Polya 和 John H. Conway

**写作技巧:**

*   [论写好](https://www.amazon.fr/Writing-Well-Classic-Guide-Nonfiction/dp/0060891548/ref=sr_1_1?ie=UTF8&qid=1483802682&sr=8-1&keywords=on+writing+well) —威廉·津瑟

**值得关注的关于 Android 特定知识和批判性思维的博客(不详尽，请在评论中建议):**

*   切特哈斯的博客。特别是[他(切特)写的关于 Android 开发的系列文章](/google-developers/developing-for-android-introduction-5345b451567c#.s6fnm0bpa)
*   丹·卢的博客
*   杰克·沃顿的博客
*   杰西·威尔逊的博客
*   西里尔·莫提尔的博客
*   [克里斯·贝恩的](http://chris.banes.me/)和[罗曼·纽里克的](http://roman.nurik.net/)关于 UI 相关主题的博客。
*   听[安卓后台](http://androidbackstage.blogspot.fr/)和[碎片化播客](http://fragmentedpodcast.com/)。
*   最后，请订阅 Android Weekly ，阅读 Android 开发世界的最新动态。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！