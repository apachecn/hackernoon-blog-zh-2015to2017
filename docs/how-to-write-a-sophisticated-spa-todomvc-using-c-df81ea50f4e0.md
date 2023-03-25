# 如何用 C#写一个复杂的 SPA: TodoMVC

> 原文：<https://medium.com/hackernoon/how-to-write-a-sophisticated-spa-todomvc-using-c-df81ea50f4e0>

如果你一直在寻找使用 C#开发单页面应用程序，否则将需要使用与 Angular 或 React 等框架配对的 [JavaScript](https://hackernoon.com/tagged/javascript) 。请继续阅读…

在本文中，我们将利用 Bridge.Net(一个 C# JavaScript trans piler)和 SharpBindings(一个通过 DOM 创建 HTML 元素的 DSL)来让[用纯 C #代码实现众所周知的](https://github.com/muigai-mwaura/sharpbinding-todomvc) [TodoMVC](https://github.com/tastejs/todomvc/blob/master/app-spec.md#functionality) 应用程序。

![](img/a2a36b650b48946e0999f6bf913fdee2.png)

[**[Live Demo]**](https://cdn.rawgit.com/muigai-mwaura/sharpbinding-todomvc/2b71415e/TodoMVC/Bridge/www/demo.html)

在这个过程中，我们将展示如何利用你所知道和喜爱的 C#特性，如泛型、LINQ、lambdas 等。来构建复杂的水疗功能。

## 入门指南

我们将从翻译 html 中如下定义的标题部分开始，

使用 SharpBindings 转换到 C#中，如下所示:

正如您所看到的，在静态内容方面， [HTML](https://hackernoon.com/tagged/html) 标签和属性与 C#代码之间存在一一对应的关系。然而，应用程序的动态行为要求:

> 在应用程序顶部的输入中输入新的待办事项…按 Enter 键创建待办事项，将其添加到待办事项列表中，并清除输入。一定要。trim()输入，然后在创建新的 todo 之前检查它是否为空。

为了实现这一点，我们需要按照下面的完整代码清单支持函数、事件处理程序和数据绑定:

上面提到的 todo 类是一个标准的 C#类，它有两个属性，一个是对 Todo 的描述，另一个是“IsCompleted”标志，指示它是否完成。此外，它支持标准的 INotifyPropertyChanged 接口。

ObservableCollection 是一个保存元素列表的类，每当元素:

1.  它在列表中的位置改变了吗
2.  被添加到列表中
3.  从列表中删除

应注意以下非显而易见的要点:

1.  我们将 input 元素的“value”属性绑定到一个函数，因为编辑 todo 的描述会随着用户交互和编程事件的变化而变化，并且需要相应地重新评估。
2.  我们引入了一个绑定到编辑 todo 的“watch”属性。每当编辑 todo 发生变化时，我们都会重新评估输入元素的数据绑定，并且只更改 DOM 中需要更新的方面。

以上两者的结合确保了输入在这行代码运行后被清除

接下来是页脚部分，在 html 中定义如下:

使用 SharpBindings 转换为 C#的过程如下:

HTML 标记和属性与 C#代码之间仍然有很大的相似之处，但是这次的动态行为比 header 部分更加广泛。根据规范:

> "当没有待办事项时，应该隐藏…#页脚。"
> 
> " Display…以复数形式显示活动待办事项的数量。确保数字被一个**标签所包围。还要确保项目词的复数形式正确:0 个项目，1 个项目，2 个项目。示例:还剩 2 项。”**
> 
> “清除已完成的按钮…单击时删除已完成的待办事项。当没有完成的待办事项时，应该隐藏。
> 
> “需要路由…应实现以下路由:#/(全部—默认)、#/活动和#/已完成…当路由更改时，应在模型级别筛选待办事项列表，并应切换筛选器链接上的选定类。当项目在过滤状态下更新时，它应该相应地更新。例如，如果过滤器处于活动状态，且该项目已被选中，则应将其隐藏。请确保在重新加载时保持活动筛选器。

这些要求按照下面列出的完整代码实现:

上面的 FooterSection 函数有四个参数:

1.  todos 的 ObservableCollection(与标头部分相同)
2.  一个返回活动待办事项数量的函数
3.  规范中描述的过滤器(所有、激活和完成)
4.  一个名为“urlTrigger”的可观察对象，它将在 url 更改时发出更改通知

应注意以下不明显的一点:

1)不是像在 HTML 模板中那样写出每个过滤器 URL:

我们将过滤器指定为项目的集合，每个项目都将使用提供的模板进行呈现:

最后，我们有主要部分，在 html 中定义如下:

使用 SharpBindings 转换为 C#的过程如下:

然后，我们需要按照规范实现以下动态行为:

> "当没有待办事项时，#main…应该隐藏。"
> 
> “将所有待办事项标记为完成…复选框将所有待办事项切换到与自身相同的状态。请确保在单击“清除已完成”按钮后清除选中状态。选中/取消选中单个待办事项时，也应更新“全部标记为完成”复选框。当所有的 todos 都被检查时，它也应该被检查。”

这些要求按照下面列出的完整代码实现:

上面的 MainSection 函数有三个参数:

1.  todos 的 ObservableCollection(与标头部分相同)
2.  一个返回活动待办事项数量的函数
3.  一个模板函数，接受一个 todo 项并返回一个呈现的 HTML 元素，如下所述。

待办事项模板在 html 中定义如下:

使用 SharpBindings 转换为 C#的过程如下:

然后，我们需要按照规范实现以下动态行为:

> “项目
> 
> 待办事项有三种可能的交互:
> 
> 单击复选框，通过更新其完成值并切换其父级
> 
> *   上的类 completed，将 todo 标记为完成
> 
> 双击
> 
> *   将鼠标悬停在待办事项上会显示移除按钮(。销毁)
>     
>     
>     
>     编辑
>     
>     
>     
>     当编辑模式被激活时，它将隐藏其他控件，并显示一个包含 todo 标题的输入，该标题应该是焦点(。focus())。编辑应该保存在模糊和回车上，并且应该删除编辑类。一定要。trim()输入，然后检查它是否为空。如果它是空的，todo 应该被销毁。如果在编辑过程中按下了 escape 键，那么编辑状态应该保持不变，任何修改都将被放弃。"

这相当复杂，需要大量代码来实现，如下所示:

上面的 TodoTemplate 函数有三个参数:

1.  从待办事项列表中删除待办事项的操作
2.  一个名为“urlTrigger”(与页脚相同)的可观察对象
3.  一个当前过滤器，将跟踪用户选择的过滤器，如页脚所述。currentFilter 将根据筛选条件确定要显示哪些待办事项。

应该注意下面不明显的一点

*   每个呈现的 todo 项目的类别(隐藏、已完成、编辑或以上都不是)，取决于
    a)项目的状态(活动或已完成)
    b)所选过滤器
    c)是否处于编辑模式
    因此，我们需要合并来自如下三个源的改变通知信号:

*   编辑模式的启动如下:
    a)用户双击待办事项的标签/描述
    b)标签通过将“isEditMode”设置为真来响应
    c)这触发编辑文本框的显示

为了将所有这些结合在一起，我们有一个“主”函数(应用程序的入口点)，定义如下:

现在你有了它，一个通过 Bridge.NET 和 SharpBindings 将 TodoMVC 规范翻译成 C#的相当简洁的字面翻译。

注意事项:

1.  还不支持持久性的实现，因为将 C#类序列化为 JSON 似乎是 Bridge.NET 正在进行的一项工作。将实现它，并在 JSON 序列化支持可用时尽快更新文章。
2.  项目源代码可以从 Github [这里获得。](https://github.com/muigai-mwaura/sharpbinding-todomvc)
3.  TodoMVC 的 [Scala 实现](https://github.com/ThoughtWorksInc/todo/blob/master/js/src/main/scala/com/thoughtworks/todo/Main.scala)从中获得了灵感。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！