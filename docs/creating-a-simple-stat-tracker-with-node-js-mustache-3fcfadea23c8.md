# 使用 MustacheJS、MongoDB、NodeJS 和 Mlab 的提示和记号。

> 原文：<https://medium.com/hackernoon/creating-a-simple-stat-tracker-with-node-js-mustache-3fcfadea23c8>

这是我已经做了一段时间的项目，使用了几种不同的技术。

1)节点-后端

2) MustacheJS —前端

3)数据库— MongoDB & Mongo Compass & Robo3T

4)部署— Heroku 和 Mlab

下面是我遇到的一些问题，以及如果您正在计划一个类似的项目，或者决定使用这些技术中的一种，需要记住的一些注意事项。

**日期&时间戳:**我最近在这个项目中遇到的一个问题是 Mustache 无法进行任何日期格式化。解决这个问题的方法通常很笨拙。据我所知，handlebars 比 mustache 具有更好的日期格式，因此如果您决定使用需要某种日期或时间戳的模板引擎，Mustache 可能不是您的最佳选择。

**引用&人口:**我在这个项目中发现的另一个问题是关于引用和人口。我所做的唯一引用是一对一的关系，我可以通过在模型中包含引用来做到这一点。我还试图做一个类似的参考和群体，其中有一对多和多对多的关系。这被证明是更困难的，我可以解决这个问题，但是我发现 MongoDB 中的这些复杂操作并不总是工作得很好。如果您计划一个需要数据库的项目，并且数据库集合/表需要多个关联，那么使用关系数据库可能比使用基于文档的数据库更容易。

**使用 mlab 进行部署:** mlab 提供了与我的应用程序的轻松集成。在我尝试连接 mlab 时，我的模型和路线已经创建好了，所以一旦我的 mlab 帐户设置好了，就只需要改变连接和端口。我强烈推荐它用于任何 MongoDB/Heroku 项目。沙盒数据库是免费的，存储空间有限，对我来说很好。如果您的项目是一个协作项目，添加额外的用户也很容易。

数据库可视化:我实际上研究了 Mongo 提供的其他产品，比如 Compass 和 Atlas。我最初打算将 Atlas 用于我的在线数据库，但选择了 Mlab，因为如果你已经将你的应用程序部署到 Heroku，我已经部署了，那么使用 Mlab 似乎更容易。我认为价格和结构是相似的。不过，我确实有机会使用 Mongo Compass 进行本地数据库可视化。在 Compass 之前，我一直用 Robo3T 做数据库可视化。我只想说，没有可比性。Robo3T 非常简约，Compass 可以让你看到更多，并且更容易地与本地数据库进行交互。

无论如何，这些只是我一路走来发现的一些问题和发现。希望这些信息对您有所帮助。下面是一个已部署的 stat-tracker 的链接，所以你可以注册，测试它，并给我一些反馈。我的长期目标是建立一些可视化的统计数据，这样你就可以看到一个图表，显示你什么时候做某些活动，或者你在某些活动上花了多长时间。但是任何关于这个简单版本的反馈都将非常感谢。我还包含了 git hub，所以也可以随意查看。谢谢！

 [## 统计跟踪器

### 编辑描述

mysterious-sea-17562.herokuapp.com](https://mysterious-sea-17562.herokuapp.com/api/splash) [](https://github.com/ethanjarrell/stattracker2) [## ethanjarrell/stattracker2

### 在 GitHub 上创建一个帐户，为 stattracker2 的开发做出贡献。

github.com](https://github.com/ethanjarrell/stattracker2) [](https://www.linkedin.com/in/ethanjarrell/) [## 伊桑·贾雷尔|职业简介| LinkedIn

### 查看 Ethan Jarrell 在 LinkedIn 上的职业简介。LinkedIn 是世界上最大的商业网络，帮助…

www.linkedin.com](https://www.linkedin.com/in/ethanjarrell/)