# 这就是我如何为 GitShowcase 选择堆栈的

> 原文：<https://medium.com/hackernoon/this-is-how-i-chose-the-stack-for-gitshowcase-fd4d49891ba0>

## 寻找业务时选择技术组合。保持冷静，享受旅程！

![](img/54862112d1d13f771c99709057340f28.png)

[Courtesy of Jake Ingle — Unsplash](https://unsplash.com/photos/s-t1oJXKYI4)

为你的新项目选择技术是一件困难的事情！有大量的框架可供选择。您可能还需要持续集成(测试、代码质量)、发送电子邮件活动、存储库管理(GitHub)和服务器等工具。

由于这是一个附带项目，我在选择使用什么技术时有很大的自由与 [GitShowcase](https://www.gitshowcase.com/) 。在这篇文章中，我将介绍选择堆栈每个部分的过程。

[](https://www.gitshowcase.com/) [## GitShowcase

### 开发人员，在即插即用产品组合中展示您的最佳项目。最棒的是，免费的。

www.gitshowcase.com](https://www.gitshowcase.com/) 

# 做出选择

## 框架

这些年来，我参与了许多框架非常不同的项目。也就是说，在我看来，Ruby on Rails 拥有最好的开发者体验*。使用`rails g scaffold`和`rails new`创建一个全面的项目轻而易举。*

因为 Rails 使用 Ruby，所以它提供了很大的灵活性和开发友好性。它可能不是性能方面的最佳选择*(与 Go、Elixir 或 Scala 相比)*，但我强烈建议您在开始下一个项目之前看一看它。

> Laravel (PHP)在这个项目中也受到了高度重视，但是 Rails 赢得了这场战斗，因为它是一个非常棒的搭建工具，而且 Ruby 比 PHP 简洁得多。

Ruby/Rails 社区非常棒，有大量令人敬畏的宝石可以加速开发过程。一旦 Rails 项目建立起来，我就能够为我的应用程序的重要部分即插即用 gems，比如身份验证、GitHub API 和 web inspection。

## 前端

我认为这是最棘手的部分。随着 node.js 最近的进展，每天都有大量的框架、捆绑器和语言被创建 *(Typescript、Dart、ES6、Elm、ReactJS、AngularJS…)* ，这是最难的选择之一。

我决定选择带有 Coffescript 的老式 jQuery，因为它的 sintax 与 Ruby 有一些相似之处。

> [背景中的声音] - jQuery？真的吗？它是旧的！
> 
> [我] -老了？？jQuery 太棒了！

jQuery 非常容易使用，并且与后端生成的页面交互良好。为了提高性能，TurboLinks 采用了不重新加载页面的技巧，因此该应用程序看起来像一个 SPA(单页应用程序),而没有构建两个完全独立的应用程序的缺点。 *(PS。它已经为你配置好了:O)*

我很欣赏 ReactJS 和 VueJS 这样的框架的力量，它们使构建单页应用程序更加容易、稳定和健壮，但它们也大大增加了项目的复杂性。

## 选择服务器

*我们的目标是编码和发布产品。*

选择 Heroku 是显而易见的。我不喜欢花时间为一个不存在的、因此仍然没有用户的项目配置定制服务器。我更喜欢发布，然后，如果进展顺利，我开始注意到瓶颈，我可以担心调整服务器。

我以前在 Heroku 上部署过前端和后端项目，成本效益非常好。您可以在几分钟内启动并运行您的应用程序。随着应用的增长，您可以添加插件并升级您的计划以获得更好的性能。成本可能会增长很快，你可能会考虑在数字海洋上迁移到一个 5 美元的实例，但不要忘记考虑如果你不得不在自己的服务器上手动配置一切，你会花费多少时间:)*(时间就是金钱)*。

## 选择数据库

如果你熟悉 Rails 和 Heroku，我知道你会尖叫 POSTGRESSSSS！！！

你说得对！我选择了 Postgres。不仅仅是因为易于与 Heroku 和 Rails 集成，还因为 JSON 字段类型允许我以散列的形式创建用户的技能列表。

技能列表由`[{name: mastery}]`组成，没有外键，因此它非常适合 JSON 字段。这样做让我**提高了性能**，节省了大量的数据库行*(Heroku 上的 Postgres 开销是用数据库行来衡量的)*并且降低了处理技能的复杂度。

## 选择 CI 工具

我选择在 Heroku *(来自 GitHub repo)、* TravisCI 上启用自动部署来运行测试，并在 CodeClimate 上启用代码质量。TravisCI 和 CodeClimate 对开源项目是免费的:)

还有其他工具，如 Codeship 和 CircleCI。我只是更喜欢使用 TravisCI 和 CodeClimate，因为我对它们感觉很舒服，除了它们之间的集成更容易代码覆盖之外，它们也很容易使用。

## 选择 GitHub

另一个没脑子的。GitHub 是开源项目的地方。另外，该项目是基于从 GitHub 登录和获取项目！

在我决定开源这个项目之前，我考虑过 BitBucket，因为它有一个免费层用于私人项目。最终 [Victor](https://www.gitshowcase.com/victorgaard) *(另一位联合创始人)*和我决定共享代码库将是一个与社区共享的好方法。我将在另一篇文章中更深入地探讨这个话题。

## 选择 MailChimp

MailChimp 有整个包裹。它不是最便宜的，但是使用起来肯定很棒，并且不需要太多的开发资源。他们有一个很好的活动生成器和一个易于使用的 API 来处理列表和通过 Mandrill 发送交易电子邮件。

将电子邮件营销从开发中分离出来会有很大帮助，因为它给了负责营销的人自由，并减轻了开发人员的大量工作。在早期阶段，当开发人员需要实现许多特性时，这可能是非常决定性的！

## 最后的想法

根据我的经验，我描述的堆栈肯定有很多偏差。我的建议是，你要睁大眼睛寻找新的机会和技术，但要选择你觉得舒服的。

> 总会有一些闪亮的新框架，你不必总是用最新的:P

## 帮助传播消息。

可以帮助 GitShowcase 被更多开发者听到。[gitshowcase.com](https://www.gitshowcase.com/)

GitShowcase on ProductHunt

*如果你还在，我知道你喜欢这篇文章。非常欢迎大家分享爱，点击心动:)*

![](img/02a14b46a07212cface451b89ab20fe7.png)

If you like this, remember to share the love and recommend it ❤

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)