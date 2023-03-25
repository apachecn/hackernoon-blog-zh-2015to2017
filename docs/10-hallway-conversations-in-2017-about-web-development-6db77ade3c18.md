# 2017 年关于 Web 开发的 10 次走廊对话

> 原文：<https://medium.com/hackernoon/10-hallway-conversations-in-2017-about-web-development-6db77ade3c18>

![](img/219c214779d6e7a76eca1c04bfda0943.png)

***本*条** ***原本出现在***[***dormoshe . io***](https://www.dormoshe.io/articles/10-hallway-conversations-in-2017-about-web-development-9)

在过去的五年里，web 开发专区充斥着新的框架、库、工具、语言和功能因素。web 开发领域正在获得动力，它的发展正在被记录在历史书上。上半年过后，我们可以说 2017 年延续了这条道路，正在被记为重要的一年。

本文将整理思路，聚焦 2017 年 web 发展的主要有趣、重要、有价值的走廊对话。

## 1.纱线——NPM 的黑仔？

Yarn 和 npm 的目标是一样的——成为 JavaScript 的包管理器。Yarn 是脸书、谷歌、Exponent 和 Tilde 合作开发的。使用 Yarn，我们仍然可以访问 npm 注册表。此外，我们可以更快地安装软件包，并在机器之间或在安全的离线环境中一致地管理依赖关系。当 Yarn 被用于共享代码时，它使我们能够更快、更自信地移动。很可能，Yarn 的改进在将来会被整合到 npm 中，所以两个用户都会从其他用户的改进中受益。

[](https://shift.infinite.red/npm-vs-yarn-cheat-sheet-8755b092e5cc) [## NPM vs 纱线小抄

### 关于纱线包装管理器，您需要了解的一切

红色](https://shift.infinite.red/npm-vs-yarn-cheat-sheet-8755b092e5cc) 

## 2.角度 1，2，4

Angular 是 JavaScript 领域广泛使用的框架。谷歌在 2016 年 9 月发布了 Angular 2 版本，作为 Angular 1 的重写版本。Google 为 Angular 选择了**语义版本化**方法。在版本 2 发布 6 个月后，框架的版本 4 发布了。版本 3 作为**技术跳过的一部分被跳过，**因为路由器的版本。Angular 1 称为 **AngularJS** ，Angular 2+则称为 **Angular** 。看起来 Angular 已经成功地通过了社区的测试，大量的开发者已经走上了新 Angular 的道路。

[](https://hackernoon.com/top-8-resources-to-explore-angular-4-ff2c1b42020a) [## 探索 Angular 4 的 8 大资源

### 角版 4 出来了！

hackernoon.com](https://hackernoon.com/top-8-resources-to-explore-angular-4-ff2c1b42020a) 

## 3.React 纤维——下一代 React

React Fiber 是 React 核心算法的一个正在进行的**再实现**。这是 React 团队两年多研究的成果。新 React 主要关心的是**的调度**。调度是暂停、恢复和取消更新过程的选项，以便获得其他任务的高优先级——它也被称为**增量渲染**。动画是将从此功能中获益的任务之一。这是必要的，因为如果更新过程很长，人眼会识别动画中的“滞后”。计划是一旦 **React 16.0** 将在今年晚些时候推出，就将这一重写工作交给开发者。与 Angular 不同，当 Fiber 将被释放时，旧的 React 应用程序将像以前一样继续工作。

 [## ECMAScript 6:新功能:概述和比较

### 支持常量(也称为“不可变变量”)，即不能被重新分配新内容的变量。…

es6-features.org](https://medium.com/u/d3391efe481a#Constants) 

## 7.Prepack —一个运行时优化器

Prepack 是一个优化 JavaScript 源代码的工具。可以在**编译时**而不是运行时完成的计算被消除了。Prepack 是 JavaScript 的部分求值器。Prepack **重写了一个 JavaScript 包**，导致 JavaScript 代码更有效地执行**。它正在积极开发中，仍然处于非常早期的阶段。预包装可以成为一个生命改变者。它背后有一家稳定的公司——**脸书**。他们希望将这个工具集成到 React 中，他们已经为此努力了一段时间。有很多事情要做。脸书的愿景是**利用预包装作为平台**。**

**[](https://hackernoon.com/facebooks-prepack-the-next-killer-in-the-javascript-zone-d932556ffd8c) [## 脸书的 Prepack——JavaScript 领域的下一个黑仔

### 在过去的日子里，社交网络是关于预包装的风暴。大概你还没听说吧。这是…

hackernoon.com](https://hackernoon.com/facebooks-prepack-the-next-killer-in-the-javascript-zone-d932556ffd8c) 

## 8.无服务器应用程序的 Firebase

无服务器架构指的是**显著依赖于第三方服务的应用。Firebase 是一个移动和网络应用开发**平台**。Firebase 建立在 Google 基础设施上，可以自动扩展，因此您不必担心如何满足用户需求。Firebase 让你只需编码，it **负责你的后端**和基础设施。云功能、实时数据库、崩溃报告、认证、云存储、托管、Firebase 通知和 Android 测试实验室只是让你的生活更快乐的一小部分功能。你还需要注意 Firebase 的困难，比如缺乏本地开发选项、有限的 JavaScript SDK 以及与 NoSQL 数据库的关系。**通过 [*angularfire2*](https://github.com/angular/angularfire2) 、[*react fire*](https://github.com/firebase/reactfire)*、*、 [*vuefire*](https://github.com/vuejs/vuefire) 等大量库，可以实现与 Firebase** 的轻松集成。**

[](https://hackernoon.com/how-to-build-a-product-loved-by-millions-and-get-acquired-by-google-the-firebase-story-82dab4e3e80c) [## 如何打造一款受数百万人喜爱的产品并被谷歌收购:Firebase 的故事

### Firebase 是一个令人震惊的工具。数亿人使用基于 Firebase 的应用程序。该公司开始时是…

hackernoon.com](https://hackernoon.com/how-to-build-a-product-loved-by-millions-and-get-acquired-by-google-the-firebase-story-82dab4e3e80c) 

## 9.Redux —单向数据流

网页和内容丰富、庞大、冗长且详细。传统的静态 HTML 页面让位于仪表板和有状态的动态页面。现在，仅仅将数据保存在一个对象中是不够的。页面部分之间有上下文，这是一个**大众**的食谱。Redux 是 JavaScript 应用程序的可预测状态容器。Redux 架构围绕着一个严格的[单向数据流](https://www.exclamationlabs.com/blog/the-case-for-unidirectional-data-flow/)。它的灵感来自脸书的 Flux 和函数式编程语言 Elm。Redux 是目前前端开发中**最火的库**之一。Redux 因为简单而很快流行起来。它被称为 React 的 **BFF，但它不仅适用于 React 的**。任何 JavaScript 框架都可以使用它。Angular 仍然有实现这些概念的库。

[Dan Abramov](https://medium.com/u/a3a8af6addc1?source=post_page-----6db77ade3c18--------------------------------) Talk about Redux @ [react-europe](https://medium.com/u/1ed63648fd08?source=post_page-----6db77ade3c18--------------------------------) 2016

## 10.微服务提升您的灵活性

微服务是一种**架构风格**，它将应用程序构建为一组**松散耦合的服务**。每种服务都可以专注于应用程序的子域。这种架构支持大型复杂应用程序的持续交付。它还使组织能够**发展其技术栈**，因为每个服务都是一个**自治**并且有一个定义好的 API 用于通信。微服务改善了故障隔离，消除了对单一技术堆栈的长期承诺，并使新开发人员更容易理解应用功能。这个概念的使用范围扩大了，像易贝、亚马逊和网飞这样的大公司已经在使用这个架构了。

[](https://blog.poki.com/from-monolith-to-microservices-b16bae1d6c9d) [## 从整体服务到微服务

### 我们如何从零开始重建我们的基础设施。

blog.poki.com](https://blog.poki.com/from-monolith-to-microservices-b16bae1d6c9d) 

# 结论

web 开发中的排列是向外的。我们无法摆脱这些变化。我们需要拥抱它们并不断学习。现在，你有了很多与同事进行走廊对话的内容。你可以深入探究这些话题。2017 年下半年将会是丰富而有趣的——这是肯定的。所以继续关注…

![](img/522b2e4ace3cfcecd43bba30fcf0a317.png)

***您可以关注我的***[***dormo she . io***](https://www.dormoshe.io)***或***[***Twitter***](https://twitter.com/DorMoshe)***阅读更多关于 Angular、JavaScript 和 web 开发的内容。*****