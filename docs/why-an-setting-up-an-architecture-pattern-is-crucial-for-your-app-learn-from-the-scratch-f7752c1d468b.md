# 为什么建立架构模式对你的应用程序至关重要——从头开始学习！

> 原文：<https://medium.com/hackernoon/why-an-setting-up-an-architecture-pattern-is-crucial-for-your-app-learn-from-the-scratch-f7752c1d468b>

因此，我将给出我的 2 美分，关于它的需求和我们试图用**架构**解决什么问题。因为一旦清楚了这一点，编码部分就非常简单和容易了。

(我将主要谈论 MVP(Model | View | Presenter)——一种常见但令人惊奇的模式。)

![](img/2857c0a3e9f47b6c4529c9318c769c12.png)

***我知道，我迟到了，因为在 Android 博客的海洋中有很多技术和大量文章，但实际上我没有！***

> 为什么？世界上的杰克·沃顿正在使用/构建/谈论/玩弄 MVP、MVVM、 [Android](https://hackernoon.com/tagged/android) [架构](https://hackernoon.com/tagged/architecture)组件、ROOM、Kotlin 和一个比萨饼的所有配料，但仍然有观众(就像我不久前一样的一个真正的大观众)仍然不知道一个好的架构在构建应用程序方面的好处。

![](img/8886349fb4dd748fe65a468a8edc05a1.png)

## 为什么我们需要一个架构？

一般来说，所有的架构都是为了解决我们在不使用任何架构设计和编写 ***上帝*活动**(活动/片段中的所有代码)时遇到的一些**常见问题**。

> 我们都认识一个朋友的朋友，他有一个应用程序，其中有一千行活动，这是不可维护的，所有的业务逻辑都在活动中，因为他从[堆栈溢出](http://www.stackoverflow.com)中复制-粘贴-编码。

**没有架构的问题**—

*   Android 活动与用户界面和数据访问机制密切相关。
*   一个小的变化，不得不重做我们的整个视图(活动/片段)。
*   视图——活动、片段……不容易测试，因为它们庞大的规模和框架的复杂性
*   程序员卷入了视图复杂性的斗争，而不是解决业务任务。
*   代码不能被重用、测试、调试和重构。

**一个好的建筑如何让生活变得更简单？**

*   复杂的任务被拆分成更简单的任务，更容易解决。
*   更小的对象，更少的错误，更容易调试。
*   观赏类保持整洁和优雅。
*   可测试代码——编写测试轻而易举(Aaha)。
*   耦合仍然是功能性与复杂性的最佳结合点。
*   如果需求发生了变化，通常很容易准确地指出您需要在代码中进行什么样的更改，而且影响最小。

## 为什么是 MVP？

嗯，这是非常有争议的。MVP 和 MVVM 是当今最受欢迎的两个选择。而开发商的优先权也是支离破碎的。
就我个人而言，我使用 MVP 是因为它相当简单，而且仍然服务于这个目的。MVVM 很棒，我个人并不是他的超级粉丝，因为尽管它让 Java 文件变得小巧而强大，但是它也让 XMLs 变得非常复杂。
(只是个人意见，不要杀我耶！)

## 什么是最有价值球员？

*宇宙间每个人都知道它是* ***模型*******视图*******演示者*** (告诉我我是否在说谎)**

**它允许将表示层从逻辑中分离出来，所以关于应用程序中 T21 部分是如何工作的一切都与我们在屏幕上展示它的方式分离开来。**

****观点——越愚蠢越好****

*   **通常是活动、片段或定制的安卓视图。只负责展示物品。**
*   **该视图通常由活动/片段实现，将包含对 P *表示器*的引用。**
*   **将用户交互引导至*演示者*。视图唯一能做的就是每次有接口动作时从 *Presenter* 调用一个方法。**

****模型—数据访问层****

*   **它是您的数据源，从数据库、网络调用或硬编码的对象列表中获取结果并不重要。**
*   **它控制如何创建、存储和修改数据。**

****演示者-中间一章****

*   ***视图*和*模型*之间的中间层。它从*模型*中检索数据，并将其格式化返回到*视图*。**
*   **它还决定了当您与*视图*交互时会发生什么。**

## **在 MVP 之后，测试应用程序是如何变得一帆风顺的呢？**

**MVP 允许您轻松地为模型和演示者编写独立的测试。由于模型没有对视图或演示者的任何引用，因此可以对其进行单独测试，从而允许开发人员从任何其他组件(如数据库)中抽象出模型。**

**演示者可以通过模仿*视图*和*模型*进行单元测试，这使得开发人员能够模拟不同的结果来测试演示者的不同行为。**

## **结论**

**在安卓系统中，将界面与逻辑分开并不容易，但是模型-视图-演示者模式可以更容易地防止我们的活动退化成数百甚至数千行的耦合类。在大型应用程序中，很好地组织我们的代码是非常重要的。否则，就无法维护和扩展。
有了 MVP，我们就可以实现以上所有的目标。**

## ****接下来呢？****

**在本系列的下一篇文章中，我们将在 Android 中实现模型视图呈现模式。我们将遵循一条更保守的道路，只使用规范代码，不使用 Android SDK 之外的任何库。这种方法将有助于理解 MVP 各层之间的不同关系。**

**— — — — — —如果你喜欢这篇文章**点击**那个💚— — — — — — — —如果你知道有人可能从中获取价值，请**分享** — — —**