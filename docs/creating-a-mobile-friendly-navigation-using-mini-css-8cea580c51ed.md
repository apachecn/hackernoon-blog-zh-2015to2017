# 使用 mini.css 创建移动友好导航

> 原文：<https://medium.com/hackernoon/creating-a-mobile-friendly-navigation-using-mini-css-8cea580c51ed>

在这个系列中，我们将带您了解一些基本的想法和技术，帮助您开始使用 [*mini.css*](http://minicss.org) *，并理解其背后的基本概念。*

![](img/a14793da78c86d6be9c229896e841815.png)

mini.css — Minimal, responsive, style-agnostic CSS framework

**我们将在本文中介绍:**卡片、网格和媒体模式

**难度:**中级(建议您从系列文章的开头[开始，一直到本文)](https://hackernoon.com/designing-a-simple-web-page-with-mini-css-f455e9f6403b)

[](https://hackernoon.com/designing-a-simple-web-page-with-mini-css-f455e9f6403b) [## 用 mini.css 设计一个简单的网页

### 在这个系列中，我们将介绍一些基本的概念和技术，帮助你开始使用 mini.css…

hackernoon.com](https://hackernoon.com/designing-a-simple-web-page-with-mini-css-f455e9f6403b) [](https://hackernoon.com/using-the-mini-css-card-module-and-media-object-3c5b5829d19c) [## 使用 mini.css 卡模块和媒体对象

### 在这个系列中，我们将介绍一些基本的概念和技术，帮助你开始使用 mini.css…

hackernoon.com](https://hackernoon.com/using-the-mini-css-card-module-and-media-object-3c5b5829d19c) 

假设您从一开始就已经了解了，那么您应该已经熟悉了一些更常见的概念和 [mini.css 框架](http://minicss.org)的模块。在本文中，我们将后退一步，重新设计我们的顶部导航菜单，使其更加移动友好，并利用框架中更高级的组件之一， [drawers](http://minicss.org/navigation#drawer) 。

如果你不完全熟悉这个概念，不要担心。简而言之，抽屉菜单(或汉堡菜单)是一种不会妨碍你的菜单，可以通过点击按钮进入视图，滑入屏幕并显示页面的导航菜单。它们非常受欢迎，尤其是最近，尤其是在移动设备上，那里的屏幕空间比台式机和笔记本电脑显示器要有限得多。事实上，它们非常流行和重要，以至于本文完全致力于 mini.css 的抽屉菜单。

[](https://blog.placeit.net/history-of-the-hamburger-icon/) [## 汉堡图标博客的简史

### 我们都见过汉堡图标。事实上，我们大多数人可能每天都在使用它。它已经成为一个…

blog.placeit.net](https://blog.placeit.net/history-of-the-hamburger-icon/) 

## mini.css 是如何做到的

汉堡和滑入式抽屉菜单的兴起是戏剧性的，许多设计师和开发者已经把他们自己独特的旋转和扭曲放在上面。mini.css 所做的是创建一个足够灵活的容器，可以根据每个网站的需求进行定制，并且可以填充从导航元素到媒体内容的任何内容。然而，独特的变化是，该菜单将只作为滑入式抽屉出现在移动设备上(除非您希望它在所有尺寸的设备上都是抽屉)，而它将是任何其他显示器上的普通内容容器。这给了你一些额外的灵活性，因为你可以隐藏它，根据你的喜好调整它的大小，将它合并到你的网格中，或者用它做一些非常复杂和疯狂的事情(我们并不特别建议这样做，但是如果你想的话，就去做吧)。

除此之外，抽屉完全是用 CSS 编写的(使用[一些非常聪明的技巧](https://css-tricks.com/the-checkbox-hack/)),如果你调整它使用的 [Sass 变量](http://minicss.org/customization/navigation#drawer)并重新编译框架，甚至可以进一步定制。它也很容易访问(也就是说，使用屏幕阅读器的人可以看到它)，并且不需要大量的标记就可以工作。

## 我们将会建造什么

我们已经为猫的社交媒体建立了一个导航，但是它可以更友好、更全面，并且在小尺寸屏幕上看起来不那么杂乱。重新设计将分两步完成，主要是为了避免混乱和过程中的问题。

在**的第一步**，我们将彻底改变我们的布局，在左侧添加一列导航元素，有效地更好地利用屏幕空间。我们的内容将不会被改变，除了当有足够的空间时，可能会向右推一点，并被压缩到一个不太宽的列中。

在**第二步也是最后一步**中，我们将更新 *Catbook* 以将此侧栏合并到所有三种屏幕大小的布局中，为不适合屏幕的布局制作一个抽屉，并允许在需要时将其添加回布局中。

## 添加导航栏

为了创建一个[导航栏元素](http://minicss.org/customization/navigation#navigation-bar)，我们将使用`<nav>`元素。框架已经将样式和定制嵌入其中，所以没有太多需要调整的地方。为了使它成为我们布局的一部分，我们将在中型和大型屏幕上删除内容列的偏移量，并在内容列之前添加导航列，根据需要调整其大小。一个好主意是选择 3:9 的导航和内容比例，而不是 2:10，因为这会使导航栏看起来混乱。

如果你一直跟着做，这一切都很简单，但问题是我们必须为更小的屏幕定义一个布局。现在，我们将使用`.col-sm-12`使它成为一个全宽的列，并添加`.hidden-sm`类以在较小的屏幕上隐藏它。这当然不是最佳选择，但不会打乱我们现有的布局，当我们将抽屉添加到等式中时可以解决这个问题。

在应用了上面描述的布局之后，您将得到类似这样的结果:

The updated layout of Catbook

在我们继续之前，请注意以下几点:

*   首先，我们使用了一个响应式可见性助手(`.hidden-sm`)，它只在特定的屏幕尺寸(较小的屏幕)上隐藏一段内容。
*   其次，你会注意到我们使用了`.sublink-1`类，有效地在导航栏中创建了子类别。
*   最后，我们根据需要改变了`<header>`的布局，这样它就不会在中等和更大的屏幕上以错误的方式突出。

## 创建抽屉

为了创建[抽屉组件](http://minicss.org/navigation#drawer)，我们需要添加一个按钮，将它切换到`<header>`栏，并将我们的`<nav>`元素和它的包装器变成实际的抽屉。为了做到这一点，我们必须做到以下几点:

*   在我们的`<header>`中添加一个带有`.drawer-toggle`类的`<label>`元素，它将作为我们的抽屉菜单的切换按钮。
*   在我们的`<nav>`元素的包装器之前创建一个`<input type="checkbox">`，并将其链接到我们创建的标签。
*   将`.drawer`类添加到我们在上一步中创建的左栏中，并删除`.col-sm-3`和`.hidden-sm`类，有效地确保抽屉样式是显示抽屉时唯一应用的样式。
*   最后，在新创建的`.drawer`中添加一个`<label>`，将它链接到您创建的复选框，并赋予它`.close`类，这样抽屉菜单就可以关闭了。
*   作为一个额外的好处，建议将`.hidden-sm`类添加到`<header>`中除了`.logo`之外的所有内容中，这样移动用户将只看到徽标和菜单切换。你也可以使用一些网格技巧将徽标文本居中，但这完全是可选的。

完成以上所有工作后，您可以在下面的代码栏中查看我是如何完成的。我还花时间添加了一些 CSS 调整，使它在视觉上更具吸引力。

Catbook with responsive navigation

这就是今天的全部内容，但我认为理解如何正确地重新设计网站的导航以在移动设备上更具吸引力是非常重要的。敬请关注更多关于 [mini.css](http://minicss.org) 的酷招和教程！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)