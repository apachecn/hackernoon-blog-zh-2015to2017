# [第 4 天]30 天内从零到 MVP 用 Javascript 构建 Web 和桌面应用程序

> 原文：<https://medium.com/hackernoon/day-4-zero-to-mvp-in-30-days-building-a-web-and-desktop-app-with-javascript-fb208846ace6>

这不是一个深入的指南，而是一个为 web 和桌面构建时要考虑的事项的集合。

在这个系列中，我将验证一个新的想法。在这里阅读概念[(第 0 天)](/@EmilBruckner/day-0-zero-to-mvp-in-30-days-what-its-all-about-c39215a531f7)和想法[(第 1 天)](https://hackernoon.com/day-1-zero-to-mvp-in-30-days-idea-plan-69db96f62b3f)。

# UX:用户之旅

随着平台越来越多，创造良好的用户体验变得越来越困难，所以我需要花更多的时间来思考这个问题。每个用户都会在某个时间到达 findbetterquestions.com。他们将有机会报名。接下来是一个空的仪表板。最糟糕的是:你甚至无法填满它，除非你下载了桌面应用程序。我希望不会有太多的人被困在这条路上。一旦有人注册，他们的目标就是下载应用程序。

## findbetterquestions.com 甚至需要一个网络仪表板吗？

不完全是，但是没有必要不提供它。很明显，你需要桌面应用程序的全部功能。这个网络应用程序仍然可以做大多数事情，比如查看已经创建的列表，以及使用几乎所有其他功能。但是，即使我可以从桌面应用程序中复制所有组件，这里仍然有更多的工作要做，所以第一个版本不会有很多 web 功能。无论如何，你都需要桌面应用，所以网络应用只是一个不错的选择。我们正在打造一个 MVP。我不能忘记那件事(我有时会忘记)。

![](img/614c4eef163a14c797834c12ca5d06cc.png)

# 构建应用程序

我还没有最终决定使用哪一个堆栈，但是我希望桌面和 web 尽可能保持一致，因为这样可以更容易地构建两个平台。具体来说，findbetterquestions 将在所有平台上使用相同的前端，甚至后端也将按计划保持不变。我们将在这里使用一个托管的 GraphQL 后端，这意味着离线支持将是有限的，但列表的创建无论如何都只能在线工作，所以这对于 MVP 来说完全没问题。我做了一些关于离线优先同步的研究，但是这似乎不值得。
电子应用程序将基于[电子反应样板](https://github.com/chentsulin/electron-react-boilerplate)。

我目前仍在尝试很多。我觉得我现在真的需要加快速度了。我已经落后于计划了。这个周末我没有做任何营销，在产品方面也没有太大的进展。

## 明天的计划

最终得到一个非常基本的工作版本。

> [←第三天](https://hackernoon.com/day-3-zero-to-mvp-in-30-days-first-doubts-pivot-1fae3d7f19c8)
> 
> [→第五天](/@EmilBruckner/day-5-zero-to-mvp-in-30-days-how-to-validate-67cc7f0f76b8)