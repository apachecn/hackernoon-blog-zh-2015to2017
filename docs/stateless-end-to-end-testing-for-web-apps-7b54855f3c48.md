# 使用 Chrome、Lighthouse 和 Treo 对 web 应用进行无状态端到端测试

> 原文：<https://medium.com/hackernoon/stateless-end-to-end-testing-for-web-apps-7b54855f3c48>

![](img/2e9670b7f966c1473789b3351211dc6b.png)

Photo by [Khai Sze Ong](https://unsplash.com/photos/ipyxkiJtdYI)

本文介绍了使用 [Google Chrome 的 Lighthouse](https://github.com/GoogleChrome/lighthouse) 和自动快照进行无状态端到端测试的想法。这种方法支持快速开发体验，并测试可用性、性能和质量回归。对于实际的例子，本文使用了 [Treo](https://treo.sh/) 。

在我们开始之前，让我们定义端到端测试，为什么它们是有价值的，为什么它们是难以开发的。

![](img/6fdb93147e6dca9c23ea484f1c3eec6d.png)

Testing Pyramid (source [*Martin Fowler*](https://martinfowler.com/bliki/TestPyramid.html)*, triangle* authorship[Kent C. Dotts](http://slides.com/kentcdodds/testing-workshop#/4/8))

**端到端测试通过像真实用户一样使用系统来评估系统**。与单元测试相比，它们更慢也更昂贵。但是他们通过验证整个系统依赖链带来了最大的信心。

让我们用 E2E 测试来分析这两个挑战。

端到端测试**慢**。启动新的浏览器实例并加载页面需要时间。一个测试可能需要 20 秒，如果按顺序执行多个测试，可能需要几分钟。

E2E 测试很昂贵或者很难开发和维护。首先，开发人员必须配置 selenium 或其他工具。处理缓慢的反馈循环和模糊的错误消息。部署到 CI，这也是相当具有挑战性的。一个月后，这些测试开始失败，没有人了解它们是如何工作的，如何调试它们。我看到这个故事在不同的项目中不断重复。

我们需要更好的东西。我们应该选择哪种**取舍**来克服这些挑战？我们如何使端到端测试简单快速，但仍然有价值？

# 无状态端到端测试

根据我的经验，**80%的端到端测试值来自初始页面加载和页面上的检查数据。真实用户使用浏览器与生产环境进行交互。因此**如果从端到端测试，它必须是一个实际的部署和一个真正的浏览器。****

****定义**:无状态端到端测试不会改变状态，允许并行执行和实际部署验证。**

****主要思想**:将一个 web 应用程序描述为一组公开可用的 URL，对每个 URL 执行 [Lighthouse](https://github.com/GoogleChrome/lighthouse) 审计，将结果与之前成功运行的结果进行比较，以检测回归。**

**如果我们不为每个测试重置环境，我们可以并行执行测试。它极大地改善了反馈循环，并允许使用生产/试运行部署。**

**如果我们测试一个实际的部署，我们可以保证整个系统的可用性。(网络服务器、CDN、数据库、第三方服务)**

**如果不写手工期望，我们使用 Lighthouse ( [和数百名开发人员的经验](https://github.com/GoogleChrome/lighthouse/graphs/contributors))来评估页面属性并将其保存为快照。下次我们部署新版本时，我们可以确保页面与之前正确的评估相比没有变化。**

**值得一提的是，在新的部署**之后，所有的关键页面都可用，并且像以前一样运行**。用户可以打开页面并开始与之交互。为了控制整个体验，监控运行时异常(用[滚动条](https://rollbar.com/)或[哨兵](https://sentry.io))并立即修复它们。**

**没有一种测试能够保证 100%的防错性。我们需要在开发努力和价值之间保持平衡。通过使用无状态的端到端测试，我们以最小的努力获得了巨大的价值。根据我的经验，这种方法非常适合中小型项目。大型项目通常在监控、QA 和定制流程方面投入巨大。**

# **使用 [Treo](https://treo.sh/) 的示例**

**[Treo](https://treo.sh/) 是用于 [Lighthouse](https://github.com/GoogleChrome/lighthouse) 的云基础设施，提供简单的端到端测试流程，并与 Slack & Github 集成。**

**使用 Treo 端到端测试设置只需点击几下鼠标。要执行测试，只需调用一个 API，并在不到 20 秒的时间内接收 Slack 或电子邮件的状态更新。**

**![](img/16b9a6d71e9118ebf58d525a6c27ca3a.png)**

**A testing flow with Treo**

## **个人网站示例:[alekseykulikov.com](https://alekseykulikov.com)**

**让我们来看一个典型的个人网站:[https://alekseykulikov.com](https://alekseykulikov.com)。它包含三个页面:主页、博客和未找到。**

**![](img/ad8255e12af7969a75c8d45464645fba.png)**

**Typical personal website with three pages: Home, Blog, 404**

****为什么要测试这个简单的网站？**因为我是本地开发的，用 git push 部署。这是三个步骤:构建，上传到 S3，使 CloudFront 发行版无效。我希望这个网站是可用的，性能和可访问的。我不想通过点击并运行每个页面的 Lighthouse 审计来手动验证部署。只是**一推就忘了**。**

**循序渐进教程([视频](http://recordit.co/aRY5nyxrcj)):**

1.  **使用 Github 账号登录 [https://treo.sh](https://treo.sh) (免费试用~500 审核)。**
2.  **点击“添加新测试套件”并命名；**
3.  **添加三个带有标题和 URL 的页面；**
4.  **点击“创建”，等待几秒钟，看看结果；**
5.  **使用来自 Git 的自动消息复制 curl 命令来部署脚本。**

```
MESSAGE=`git log --format=%B -n 1 $sha1 | xargs`
curl [https://api.treo.sh/v1/suites/1](https://api.treo.sh/v1/suites/8) \
  -H "Authorization: Bearer xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" \
  -d message="$MESSAGE"
```

**下一次，我在 [/blog](https://alekseykulikov.com/blog) 上做了更改并破坏了可访问性，Treo 通知我:**

**![](img/6e318c6a1a1e554a5f942d13e3f82bcc.png)**

**Failed Report**

## **Web 应用示例: [treo.sh](https://treo.sh)**

**Treo 使用自身进行测试。这是一个有 16 个不同页面的 SPA。测试过程类似于第一个例子，但有一些改进。在美国/欧盟地区，它使用 cookies 进行身份验证，并使用特殊页面进行性能监控。**

**![](img/66862eff74f64edb7cf21683282d66f0.png)**

**Examples of [treo.sh](https://treo.sh) UI**

**用一组 URL 来描述一个 SPA 是很自然的。通常，每个 URL 都是一个路由器条目。每个条目都是具有自定义逻辑的独立视图。加载视图与前一次运行没有任何不同，这为它继续工作提供了可靠的保证。**

**在 Treo 的开发过程中，我发现了很多使用 Treo 的 bug。损坏的页面、失败的 Webpack 构建、可访问性和性能退化。这让我对这种方法充满信心。对于投入的时间来说，无状态端到端测试是最有用的一种测试。接下来，进行单元测试，但这是[完全不同的故事](https://github.com/testdouble/contributing-tests/wiki/Testing-Pyramid)。**

**我期待听到来自社区的**反馈**！让我们在 twitter [@alekseykulikov_](https://twitter.com/alekseykulikov_) 或评论中讨论一个无状态端到端测试的想法。**

**同样，它**不是**通用的方法。对于中小型项目来说，这是实现更好的质量和更快的发布周期的一组权衡。**

**如果您在配置 Treo 方面需要任何支持，请联系我: [info@treo.sh](mailto:info@treo.sh) 。我很乐意帮助您的团队定义最重要的网页，并期待灯塔的结果。**

**让我们构建可靠的 web 应用程序。测试愉快！👍**