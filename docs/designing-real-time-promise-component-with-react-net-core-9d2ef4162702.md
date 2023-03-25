# 用 React +设计实时承诺组件。网络核心

> 原文：<https://medium.com/hackernoon/designing-real-time-promise-component-with-react-net-core-9d2ef4162702>

考虑下面的场景，其中 web 应用程序中的 UI 组件依赖于后端的某个资源，但是组成该资源的数据必须从几个不同的来源聚合，可能是通过异步调用从一些微服务聚合。

在这个场景中，假设我们希望 UI 显示一个初始的加载屏幕，然后这个屏幕会变成一个实时的进度状态来报告后端发生了什么。如果一切顺利，当接收到聚合数据时，它最终会呈现所需的组件。

我的信号/基于 NET Core 的库， [dotNetify](http://dotnetify.net) ，为我提供了与这个场景所需的 [React](https://hackernoon.com/tagged/react) 组件建立异步实时通信的方法；然而，代替传统的命令式风格，我想让它具有声明性和可重用性；类似于 Promise 对象的东西——但是是一个组件——实际上是为尚未收到的值安排另一个组件的显示。

## 基本承诺

这是组件的初始设计:

```
<Promise **viewModel**="MyComponentVM">
   {resp => <MyComponent {...resp} />
</Promise>
```

通过使用这种声明式风格，可以简洁地表达意图:当 Promise 组件被呈现时，它将向后端发起一个请求，以获取呈现`MyComponent`所需的数据，并在收到响应时呈现它。

使用 dotNetify，通过与后端的富视图模型建立实时连接来完成组件的水合。我们可以让视图模型有状态，这样它就可以跟踪构建响应负载所需的多个异步调用，并在完成时将其发送到我们的前端。

## 承诺与进步

为了允许进度报告，我们将使组件接受更多的属性:

```
<Promise **viewModel**="MyComponentVM"
         **isResolved**={resp => resp.Result}
         **while**={resp => <ProgressComponent steps={resp.Steps} />}
   {resp => <MyComponent {...resp} />
</Promise>
```

`isResolved`属性接收一个用于决定何时渲染`MyComponent`(当响应包含`Result`)的函数，`while`属性接收一个渲染`ProgressComponent`的函数。事实上，我可以像这样将一个组件传递给另一个组件，这是一个非常酷的特性。有了 React，您的组件就是一等公民。

总结一下:在呈现时，Promise 组件将与`MyComponentVM`视图模型建立实时连接，该视图模型将在一段时间内发送多个响应有效载荷，包括完成的进度步骤和最终的结果。该组件将呈现传入的`ProgressComponent`,以显示到目前为止已完成的步骤，同时等待最后的有效负载。当它收到时，它切换到渲染`MyComponent`并实现它的承诺。

## 视图模型

后端视图模型用 C#编写，运行在多平台 ASP.NET Core 上。它从 dotNetify 的基类`BaseVM`继承而来，这个基类为它提供了指定哪个属性已经更改的 API，以及将更改的属性推送到前端的 API。例如，下面的代码片段将把`Result`属性的值推送到前端组件:

```
**Changed**(nameof(Result));
**PushUpdates**();
```

我们将使用[react vex](http://reactivex.io/)技术将多个异步数据请求组合成视图模型可以订阅的单个数据流。提供的 API 允许我们一个接一个地接收完整的请求(`OnNext`)，并在收到所有响应时得到通知(`OnCompleted`)。

## 承诺实现

下面是 Promise 组件的基本实现:

当出现网络错误或后端请求本身出现问题时，您可以通过添加显示错误消息组件的机制来轻松地使这一点更加健壮。

在`Promise`文件夹下的[https://github.com/dsuryd/dotnetify-react-demo](https://github.com/dsuryd/dotnetify-react-demo)查看一个工作演示。

![](img/1a1bfbe8e9968921ca8e7ef6c7d787ad.png)

尽管这个 Promise 组件是不可链接的，但是链接的需要(通常涉及数据聚合或转换)通过在后端视图模型中完成该逻辑而被有效地否定了。

## 摘要

为您的 web 应用程序从多个异步来源获取数据始终是一个挑战，并且会增加您的前端代码，而这些代码实际上并不是特定于 UI 的。这里显示的[编程](https://hackernoon.com/tagged/programming)技术带来了显著的优势:

*   包含承诺的声明性方式使您的代码简洁、可重用、易读。
*   将数据聚合/转换推到后端会使您的前端更干净、更精简，并鼓励关注点的分离。
*   内置的异步实时通信允许更健壮的用户交互，如真实的进度报告，并为更具创造性的方法开辟了道路。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)