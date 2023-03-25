# 为什么 CSS 网格比 Bootstrap 更适合创建布局

> 原文：<https://medium.com/hackernoon/how-css-grid-beats-bootstrap-85d5881cf163>

![](img/a102c342bd48a07cb547c6e81348d070.png)

CSS 网格是一种在网络上创建布局的新方法。我们第一次在浏览器中有了一个合适的布局系统，这给了我们很多好处。

如果你将 CSS Grid 与其中最流行的框架 Bootstrap(顺便提一下，我们已经在这里[创建了一个免费课程)相比较，这些好处会变得特别明显。您不仅可以创建以前不引入 JavaScript 就无法创建的布局，而且您的代码将更易于维护和理解。](https://scrimba.com/g/gbootstrap4)

在这篇文章中，我将解释为什么。

> 注意:我还创建了一个免费的 CSS 网格课程。[点击这里获得全部访问权限。](https://scrimba.com/g/gR8PTE)

[![](img/9886456d60caaee2af46ab48e13f6f2e.png)](https://scrimba.com/g/gR8PTE)

Click the image to get to the course.

现在让我们来看看我认为 CSS Grid 胜过 Bootstrap 的三大理由。

# 你的标记会更简单

用 CSS Grid 替换 Bootstrap 会让你的 HTML 更干净。虽然这不是最重要的好处，但它可能是你注意到的第一个好处。

为了举例说明这一点，我为一个网站创建了一个虚拟布局，这样我们就可以比较两个版本所需的代码。这是:

![](img/6f39b46fedebd3a69189cd84a08710da.png)

> 注意:我给了这个例子一点设计。然而，这与 CSS Grid 和 Bootstrap 之间的比较无关，所以我将 CSS 的这一部分保留在我的代码示例之外。

## 引导程序

让我们首先看看在 Bootstrap 中创建这个网站所需的标记。

![](img/65cfa27f2cd186be9248d1d6fa3e9534.png)

这里我想让你注意两件事:

1.  每一行都必须有自己的`<div>`标签。
2.  你已经使用类名来指定布局(`col-xs-2`)。
3.  随着这种布局越来越复杂，HTML 也越来越复杂。

如果这是一个响应式网站，标签通常会看起来更糟:

![](img/196e76e53ea43a591be1e6787bd3d4d0.png)

## CSS 网格

现在让我们看看 CSS 网格是如何做到这一点的。这是 HTML:

![](img/076cad5acc08871cb96608c1215be9eb.png)

I could have used semantic elements here, but I’m choosing to stick with div’s to make the comparison to the Bootstrap example easier.

我们可以立即看到这个标记更简单了。难看的类名和每行所需的额外 div 标签都不见了。它只是一个网格的容器，以及里面的项目。

> 与 Bootstrap 不同，这种标记的复杂度不会随着布局复杂度的增加而增加。

然而，虽然 Bootstrap 示例不要求您添加任何 CSS，但是 CSS 网格示例当然要求这样做。具体来说，您必须添加以下内容:

![](img/05e55343620a47cdfc61dc4c4305f865.png)

对于一些人来说，这可能是一个支持 Bootstrap 的论点:你不必担心 CSS 来创建一个简单的网格，因为你只需在 HTML 中定义布局。

但是正如您将在下一点中理解的，标记和布局之间的这种耦合实际上是灵活性的一个弱点。

# 更加灵活

假设你想根据屏幕大小改变你的布局。例如，在手机上查看菜单时，将菜单拉至顶行。

换句话说，改变布局如下:

![](img/6f39b46fedebd3a69189cd84a08710da.png)

变成这样:

![](img/dceb3fcc3cc17f52550f12c0538f3a5f.png)

## **CSS 网格**

用 CSS Grid 做到这一点超级简单。我们只需添加一个媒体查询，然后根据需要随意排列项目:

![](img/5da9e439d14cef5a2a7ab5ae8727a37a.png)

事实上，你可以像这样重新安排布局，而不用担心 HTML 是如何编写的，这被称为*源代码顺序独立性*，这对开发者和设计者来说是一个巨大的胜利。

> CSS Grid 允许你把 HTML 变成本来的样子。内容的标记。不是视觉效果，它属于 CSS。

## 引导程序

如果我们想在 Bootstrap 中做同样的事情，我们实际上必须改变 HTML。我们必须将**菜单**标签移到第一行，除了**标题之外，因为**菜单**被限制在第二行。**

![](img/14a5eb8817693ec098bf66671c38b4d1.png)

基于媒体查询来做这件事并不简单。仅仅使用 HTML 和 CSS 是做不到的，所以你必须开始使用 JavaScript。

这个例子说明了到目前为止我使用 CSS Grid 的最大好处。

# 不再有 12 列的限制

这不是什么大问题，但它已经困扰了好几次。由于引导网格被分成 12 列，如果你想要一个 5 列的布局，你会遇到麻烦。或者七个。或者九个。或者任何加起来不到 12 的东西。

对于 CSS Grid，情况并非如此。您可以让您的网格拥有您想要的列数。这是一个七列网格。

![](img/1eb3835d2a0e32fc823f3b5d4284ea54.png)

这通过将`grid-template-columns`设置为`repeat(7, 1fr)`来完成，如下所示:

![](img/65b2f5a3e93ce3c4bf5231e4e5b13484.png)

现在可能有一种方法可以破解 Bootstrap 并使其工作。

我知道 Bootstrap 4 使用 Flexbox，这也使这成为可能，但它仍未退出测试。

## 浏览器支持

在结束之前，我们当然还得谈谈浏览器支持。在写这篇文章的时候，全球 75%的网站流量支持 CSS 网格。

![](img/2043360740e4eb5a3ed2537f8355386e.png)

然而，在你完全放弃使用 CSS 网格的想法之前，我想听听 [Morten Rand-Eriksen](https://www.smashingmagazine.com/2017/06/building-production-ready-css-grid-layout/) 对这个主题的看法，他认为 CSS 网格是一个重新构建我们关于向后兼容性的思考方式的机会:

> CSS 网格是一个**布局**模块；它允许我们改变一个文档的布局而不影响它的源代码顺序。换句话说，CSS grid 是一个**纯可视化的**工具，如果使用正确，它应该对文档内容的交流没有影响。由此得出一个简单但令人惊讶的事实:旧浏览器缺乏对 CSS grid 的支持不应该影响访问者的体验，相反**只是让体验变得不同**。

换句话说，因为你已经把内容和视觉分开了，结果是所有的访问者都会看到内容，然而，CSS Grid 会通过更好的布局来增强那些支持它的人的体验。

## 结束语

最后，我想分享 Mozilla 的开发者倡导者 Jen Simmons 的一句话。她描述了我在了解 CSS Grid 后对它的同样看法:

> 我使用 CSS Grid 越多，我就越确信在其上增加一层抽象层没有任何好处。CSS Grid 是布局框架。直接嵌入到浏览器中。—珍·西蒙斯

如果你确信 CSS 网格是未来的趋势，并且你想了解更多，请点击这里查看[我的免费课程。](https://scrimba.com/g/gR8PTE)

感谢阅读！我的名字是 Per，我是 [Scrimba](http://scrimba.com) 的联合创始人，这是一个创建交互式编码截屏的新工具。如果您有任何问题或意见，请随时通过 [Twitter 联系我。](https://twitter.com/perborgen)