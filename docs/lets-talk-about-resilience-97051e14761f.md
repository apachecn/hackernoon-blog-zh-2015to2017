# 让我们来谈谈弹性

> 原文：<https://medium.com/hackernoon/lets-talk-about-resilience-97051e14761f>

## 度过停机时间并创造关键时刻体验

![](img/ef79cf48d2509400aeb00ab3be765148.png)

Fail Whale from Twitter’s Golden Years

贵公司的产品上一次停机是什么时候？它对您的客户有什么影响？

这个问题通常是开发团队视而不见的。我们希望应用程序始终保持运行，当它关闭时，我们会做出反应。这种被动的推理对我们开发者来说是公平的，但是要以用户体验为代价。

如果您是服务和应用程序开发的新手，您可能会想，*我还能做些什么来保持高正常运行时间？*

让我介绍一下*弹性*的概念。

弹性通常被定义为从失败中快速恢复的能力，倾向于弹性。在这篇文章中，我将讨论客户端和网络相关的[弹性](https://hackernoon.com/tagged/resilience)以及如何改进您当前的堆栈。

我将使用 awesome [axios 库](https://github.com/mzabriskie/axios)来说明下面的例子。

[](https://github.com/mzabriskie/axios) [## mzabriskie/axios

### 浏览器和 node.js 的基于 axios - Promise 的 HTTP 客户端

github.com](https://github.com/mzabriskie/axios) 

```
const axios = require('axios');
```

# 超时

网络是不可预测的野兽。我们无法预测连接何时以及如何断开。我们能做的就是做好准备。

*我的 app 达不到某个 API 会怎么样？反应慢一点怎么样？*

通常，开发人员会对此置之不理，因为我们希望用户总是连接到快速网络。这是一个危险的假设，尤其是当我们不知道谁是用户的时候。

为了做好准备，*总是给你的请求添加一个超时:*

```
async function MakeRequest() {
  try {
    await axios.get('/slow', {
      timeout: 5000
    });
  } catch (err) {
    // ...
  }
}
```

这可以确保用户不必为你的应用程序的响应等待很长时间。你可以和你的 UX 同事讨论适应这种情况的各种方法。我最喜欢的是实现…

# 重试

*当我的请求失败或超时时会发生什么？*

客户端和网络错误比比皆是，任何开发人员都不应忽视这一事实。有很多请求失败的场景，我们必须考虑应用程序应该如何反应。

一个好的策略是实现重试。通常的阈值是在真正失败之前重试 3 次。

```
async function MakeRequest(retry = 0) {
  try {
    await axios.get('/failing', {
      timeout: 5000
    });
  } catch (err) {
    if (err < 3) {
      await MakeRequest(retry + 1);
    } else {
      // ...
    }
  }
}
```

这确保了应用程序有足够的尝试次数来尝试到达曾经失败的端点。

*就此场景与您的 UX 同事合作，确定如何处理正在重试的临时请求和最终的正常失败。*

# 撤退

如果我的请求失败了怎么办？我应该向用户展示什么？

不可避免地会出现停机。仅仅因为你在使用 AWS，并不意味着你的应用不会失败。一切终将失败，你得有退路。

```
async function MakeRequest(retry = 0, fallback = false) {
  try {
    const url = fallback === false ? '/failing' : '/fallback';
    await axios.get(url, {
      timeout: 5000
    });
  } catch (err) {
    if (err < 3) {
      await MakeRequest(retry + 1, fallback);
    } else {
      if (fallback === false) {
        await MakeRequest(0, true);
      } else {
        // ...
      }
    }
  }
}
```

这确保了在请求失败的情况下，应用程序会收到一些东西。这不会破坏用户的体验，因为在他们看来这并不一定会导致失败。

# 记录

*我怎么知道哪个请求/屏幕/api 失败了？*

这是编写应用程序之前必须涵盖的基础知识之一。确保在您的开发和生产环境中可以进行适当的监控。这包括客户端和服务器端。

从服务器端对您的监控工具实施集中日志记录。捕获所有已处理的异常，并将它们丢到可搜索的日志中。当最坏的情况发生时，这将大大有助于您的调试工作。

也实现客户端错误处理，如果可能的话，将它们放在监控工具中的一个单独的桶中。它将帮助您确定以前无法预测的故障点。

# 断路器

*如果服务经常失败，我该怎么办？*

这一点涵盖了更多的微服务方法，在我看来，在大多数情况下你应该完全避免(在评论中问我*为什么*)。如果你已经处于这种情况，**让我们的朋友** [**马丁福勒解释一下**](https://martinfowler.com/bliki/CircuitBreaker.html) **。**

[](https://martinfowler.com/bliki/CircuitBreaker.html) [## 断路器

### 连续交付应用程序架构标签:软件系统远程调用软件是很常见的…

martinfowler.com](https://martinfowler.com/bliki/CircuitBreaker.html) 

本质上，您的应用程序需要能够选择性能良好的服务节点，并在这些服务节点关闭时保护您的服务。一个显著的好处是对可用资源的智能处理。防止不必要的 cpu 和内存使用，你可以把它分配到其他地方。

回退、重试和日志记录的组合对于实现这一点至关重要。

请阅读马丁·福勒的观点，因为他是最好的解释者。您也可以查看[网飞/海斯特里克斯](/netflix-techblog/introducing-hystrix-for-resilience-engineering-13531c1ab362)库进行参考。

[](/netflix-techblog/making-the-netflix-api-more-resilient-a8ec62159c2d) [## 提高网飞 API 的弹性

### 为每天处理十亿次请求的系统保持高可用性和弹性。

medium.com](/netflix-techblog/making-the-netflix-api-more-resilient-a8ec62159c2d) 

# 结论

最后，所有这一切都是在开发者、建筑师和 UX 之间进行的，目的是为用户提供一个好的体验。这永远是终极目标。

永远记住，UX 并没有以[设计](https://hackernoon.com/tagged/design)结束，它也是用户如何通过不同的场景体验你的应用程序。无论是故障、缓慢，甚至是停机。

我可能没有涵盖每一个角落，但我希望它能在您考虑构建下一个堆栈时，为您提供一个正确的方向。

祝你好运！

通过网飞的博客了解更多关于弹性的内容，他们那里有很棒的东西。别忘了去看看 Hystrix 和混沌猴。

[](/netflix-techblog/making-the-netflix-api-more-resilient-a8ec62159c2d) [## 提高网飞 API 的弹性

### 为每天处理十亿次请求的系统保持高可用性和弹性。

medium.com](/netflix-techblog/making-the-netflix-api-more-resilient-a8ec62159c2d) [](/netflix-techblog/introducing-hystrix-for-resilience-engineering-13531c1ab362) [## 弹性工程中的 Hystrix 简介

### 在分布式环境中，任何给定服务的失败都是不可避免的。

medium.com](/netflix-techblog/introducing-hystrix-for-resilience-engineering-13531c1ab362) [](/netflix-techblog/chaos-engineering-upgraded-878d341f15fa) [## 混沌工程升级

### 混沌金刚是迄今为止最具破坏力的混沌猴子

medium.com](/netflix-techblog/chaos-engineering-upgraded-878d341f15fa)  [## 网飞猿人军

### 保持我们的云安全、可靠且高度可用

medium.com](/netflix-techblog/the-netflix-simian-army-16e57fbab116) 

如果你在新加坡，想要这些东西，就来加入我们吧:

[](https://foxcareers.com/Search/JobDetail/FOX0003GA?organization=Fox+Networks+Group) [## 数字产品经理-订阅、支付和合作伙伴-福克斯职业

### 数字产品经理-订阅、支付和合作伙伴

- Fox Careers 数字产品经理-订阅、支付和 Partnershipsfoxcareers.com](https://foxcareers.com/Search/JobDetail/FOX0003GA?organization=Fox+Networks+Group) [](https://foxcareers.com/Search/JobDetail/FOX0003G9?organization=Fox+Networks+Group) [## Fox+ - Fox Careers 视频开发和运营经理

### 商标和版权声明:商标和福克斯及其相关实体。保留所有权利。使用本网站假设…

foxcareers.com](https://foxcareers.com/Search/JobDetail/FOX0003G9?organization=Fox+Networks+Group)