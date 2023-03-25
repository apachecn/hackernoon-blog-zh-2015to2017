<link href="../Styles/Style.css" type="text/css" rel="stylesheet">

# Angular Pro 提示:如何在中动态创建组件

> 原文：<https://medium.com/hackernoon/angular-pro-tip-how-to-dynamically-create-components-in-body-ba200cc289e6>

![](img/348e746f3ec3449a5ee06ad6696e6db4.png)

**更新:**我写了另一篇博文，解释了如何使用棱角分明的 CDK 的门户来实现完全相同的结果。请看一看[吧](https://hackernoon.com/a-first-look-into-the-angular-cdk-67e68807ed9b)。如果你对魔术不感兴趣，想知道如何从头开始，那么请继续阅读这篇文章。

# **给我看看代码**

如果你有点像我，你只是想看看代码，你会弄明白，所以你去吧。下面的服务只有一个简单的方法`appendComponentToBody`,它将把你的组件作为 body 的直接子组件添加到 Angular 的组件树中(这样它就可以包含在 Angular 的变化检测周期中)。**见本帖末尾的 working plunker。**

# **酷，但是，见鬼那是什么？**

如果您是在检查完代码后到达这里的，那么这意味着您需要对要点中发生的事情进行一些解释。

## 一点背景知识

我第一次遇到这个问题是在不久前，当时我试图将模态添加到我的 web 应用程序中。我找不到好的文档来动态地将组件添加到我的文档主体中。一些备选方案建议在`app.component.html`级别添加*助手*组件作为宿主容器，一些其他方案建议将`app.component.ts`中的`viewRef`传递给定制服务，以便`app-root`可以在以后用作锚，等等。不过，我都不喜欢。然后我前往 ng-conf 寻找答案，并向 Jeremy Elbourn (@jelbourn)询问了这个问题，他向我指出了 Angular Material 中的几个实用类。

## 导航角材料

简单地说，角材料回购是**一个好资源**地狱。如果你真的想了解如何构建健壮且编写良好的角度组件，你应该每天都学习:[https://github.com/angular/material2](https://github.com/angular/material2)。

不过，我花了一段时间才明白它们是如何将组件附加到主体上的。他们从一堆文件中调用一堆方法，但我在这里为你分解它。让我们直接开始吧。

# 分解要点

再次检查代码片段，并查看注释中的数字。我将使用这些数字进行解释。

## **1。创建组件参考**

使用`ComponentFactoryResolver`创建组件的引用。因为您将把您的组件添加到`AppRef`(参见步骤 2)，所以您需要使用构造函数中提供的 Angular 的默认注入器来创建这个组件。

**请注意**此时，您可以使用`componentRef`将数据绑定到组件的输入，如`componentRef.instance.myInput = 'yay!'`。

## 2.将组件附加到应用程序参考

在这一点上，你有了一个对你的组件的引用，但是它还没有生活在 Angular 的世界里。该组件不在 ng 组件树中，因此 Angular 不会对其运行更改检测。为了允许对我们的组件进行变更检测，我们应该将它附加到`ApplicationRef`。

## 3.从组件引用中获取 DOM 元素

我们快到了。我们有一个`componentRef`，它也生活在 Angular 的世界里。现在，我们希望看到它在页面中呈现，为此，我们必须将 DOM 元素从组件中取出。代码非常冗长，无法让 TypeScript 理解我们想要实现的目标，但基本上，我们这样做是为了获取组件的 DOM 元素:`componentRef.hostView.rootnodes[0]`。

## 4.追加组件主体

**TL；dr**使用我们在步骤 3 中得到的 DOM 元素。

**关于这个**的一些备注。据说 Angular 是一个平台，在服务器、网络工作者、移动设备(？)，等等。但是，话虽如此，我还是很难找到一个用例，在这个用例中，动态创建的组件必须呈现在服务器端或其他上下文中。我不知道它在 Ionic 中如何工作，但可以肯定的是有一个父 anchor DOM 元素，你可以用`body`替换它来实现这个功能。

## 5.(可选)用核武器摧毁它

这让您知道如何通过两步来移除组件:

1.  将视图从`ApplicationRef`上拆下，这样 Angular 就不会进行变化检测。
2.  销毁组件参考。这将自动从文档中移除 DOM 元素。

**非常重要:**您必须在主模块声明中将您的组件添加到`entryComponents`中。这将允许 Angular 动态创建组件。

**干活的扑通:【https://plnkr.co/edit/Yc1ijM6shHt2JAPi7Fdg】T22**

**使用这种方法的角插件:**【https://github.com/jdjuan/ng-notyf 

如果你喜欢，就表现出你的爱。留下评论。请通过 Twitter @caroso1222 联系我。