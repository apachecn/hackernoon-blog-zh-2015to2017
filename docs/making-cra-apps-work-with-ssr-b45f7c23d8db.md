# 让 CRA 应用程序与 SSR 一起工作

> 原文：<https://medium.com/hackernoon/making-cra-apps-work-with-ssr-b45f7c23d8db>

## 让 SSR 再次变得简单

> 我已经为 React v16 更新了这个系列的文章。旧版本 react 的代码现在在 github 上[。](https://github.com/zhirzh/cra-with-ssr-old)

这一系列的帖子都是关于在[服务器](https://hackernoon.com/tagged/server)上渲染 react 应用，这些应用是使用 [create-react-app](https://github.com/facebook/create-react-app) 构建的。github 上的代码是[。](https://github.com/zhirzh/cra-with-ssr)

## [第 1 部分:简单起步](/@zhirzh/making-cra-apps-work-with-ssr-part-1-1e23d6b1603d)

从一个甚至不能处理 CSS 的准系统应用程序开始。不过，这的确有助于解释其中的过程。

## [第 2 部分:添加对静态资产的支持](/@zhirzh/making-cra-apps-work-with-ssr-part-2-ff5f644e9d24)

一旦基础系统启动并运行，就该让它与 CSS(和其他资产)一起工作了。

## [第三部分:集成冗余](/@zhirzh/making-cra-apps-work-with-ssr-part-3-c981adb8bdab)

毫不奇怪， [*redux*](https://hackernoon.com/tagged/redux) 是数据管理的流行选择，在服务器渲染管道中必须对其提供强有力的支持。

## [第 4 部分:使用 react-router 进行路由](/@zhirzh/making-cra-apps-work-with-ssr-part-4-2954d04ea67c)

我们将探索 react-router 的静态和动态路由，以及处理路由参数和 redux 集成。

# “为什么”和“如何”部分

自从我在 2015 年遇到 [React](https://reactjs.org/) 以来，我就喜欢和它一起工作。这次经历是一次巨大的范式转变。从杂乱无章的 jQuery 到更加精细的 jQuery。

最终，我开始感受到疲劳的影响。开始每一个新项目都需要大量的体力劳动，起初看起来很小，但规模迅速扩大。

我开始寻找解决办法。后来，我找到了 [create-react-app](https://github.com/facebookincubator/create-react-app) 。

## 创建-反应-应用

CRA 是我一直在寻找的解决方案。最终，它成为了我的起点。最棒的是，它与 react 生态系统的其他项目配合得非常好。

但绝不是所有的解决方案，因为 CRA 不支持服务器渲染。还没有。🤞

## 服务器渲染

我把找到的所有零碎东西拿出来，试着把胶水粘在一起，做成能正常工作的*东西*。这个系列是我记录这个过程的方式。