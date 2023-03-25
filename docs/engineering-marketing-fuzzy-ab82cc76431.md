# 工程驱动营销@模糊

> 原文：<https://medium.com/hackernoon/engineering-marketing-fuzzy-ab82cc76431>

## 构建技术来推动销售

![](img/54a2bf50e6454c4c72da86a11622a506.png)

公司网站传统上被视为营销门户。它们是定义品牌、传达清晰信息和捕捉线索的地方。他们还可以帮助建立一个社区，并促进吸引人的内容。

然而，随着网络技术的发展，我们已经看到了网站发挥更大作用的转变。除了所有传统的营销内容，网站现在还包括账户创建、入职和其他产品流程。

所以很多初创企业问的问题是:哪个团队应该拥有网站？应该归营销团队管吗？还是应该由工程团队负责其交付？

从工程的角度来看，CTO 通常回避任何网站的参与。他们担心营销会占用他们的产品开发时间。而另一方面，工程团队可能会对公司网站产生占有欲，使得营销很难实现他们的目标。

从营销的角度来看，大多数营销人员都希望拥有工程资源。像 [Nerdwallet](https://www.nerdwallet.com/) 这样的内容驱动型网站已经表明，包含计算器、比较工具和其他软件插件等互动功能的营销内容确实有助于吸引读者，并极大地提高搜索引擎优化。这是非常酷的东西！

简单来说，网站已经成为营销、产品、工程之间的一个艰难的平衡。

# 模糊方法

在 Fuzzy，我们希望让[网站](https://www.yourfuzzy.com)和营销无缝地融入产品功能。我见过一些公司的产品与他们营销网站上的一点都不像，这导致了可怕的用户体验。许多 B2B 企业可以侥幸逃脱，但作为一个 B2C 公司，我们希望有一个干净和一致的体验。

> 本质上，网站是软件的一部分

从本质上来说，网站是软件的一部分，因此让工程团队创建一个平台来支持营销以实现他们的目标对我们来说是有意义的。这意味着工程团队将拥有网站，所以我们必须选择工程师喜欢的技术来开发网站的功能。

最初的模糊网站是用 WordPress 构建的——这是工程师最糟糕的噩梦。WordPress 故意限制你可以做的技术工作，但是它对于想要能够起草和发布内容和博客帖子的营销团队来说是非常棒的。另外，我们现有的所有内容和博客文章都已经在 WordPress 中了。

所以我们想开发一个平台，利用我们喜欢的 WordPress 的现有功能，并通过一些模糊工程来扩展网站的功能。

# 技术

大多数开发者不知道的是，WordPress 是完全开源的，并且附带了一个 [REST API](https://developer.wordpress.org/rest-api/) 。WordPress 实际上只是一个基于 MySQL 数据库的笨拙的 PHP 应用程序。所以如果你愿意写一些 PHP，你真的可以让 WP 做任何你想做的事情。

由于我们不想成为 PHP 开发人员，而且我们栈中的大部分都是 Javascript，我们决定为我们的网站编写一个独立的 [React](https://facebook.github.io/react/) 前端。我们会将 WordPress 的使用限制在仅仅作为一个内容管理系统，并使用它的 API 来显示带有我们的 React 印章的内容。

这样我们就不会中断营销团队当前的工作流程，他们可以继续在 WordPress 中开发他们的内容(使用其所有的发布和起草功能)。它还设置我们在开发像注册流程这样的功能时调用我们的内部模糊 API(以及我们希望在网站上继续发展的任何其他产品功能)。

## 结构

[React with Redux](http://redux.js.org/docs/basics/UsageWithReact.html) 在获取和显示数据方面是一个相对严格的框架，所以我们遵循了那里的大多数典型约定。方便的是，它还有一个非常好的服务器端呈现特性，当您想要返回一个完全呈现的 HTML 页面(而不是在浏览器中呈现)时，这个特性非常方便。我们希望我们所有的内容页面都呈现在服务器端的搜索引擎优化的目的。实现这一点需要几个不同的部分。首先，我们从页面的基本 React 类开始:

你会注意到*需要追加到我们的 ES6 类的*数组，以便在页面呈现时向 WordPress API 发出服务器端请求。 *pageName* 参数是从 URL 收集的。从那里，我们使用标准的 AJAX 运行以下对 WordPress 的请求:

```
GET: <wordpress_url>/wp-json/wp/v2/posts?slug=${pageName}
```

来自 WP 的内容页面然后被设置在我们的 Redux reducer 中，并在我们的内容中显示为 *this.props.page.* React 然后负责那里的渲染。差不多就是这样！

## 模糊成分

使用 React 作为前端还允许我们开发我们自己的小部件，我们的营销团队可以在我们的网站上使用。例如，让我们看看我们的 LeadCaptureWidget:

```
<FuzzyLeadCapture buttonText="Sign up" label="Join the Fuzzy Community" />
```

然后，它会在网站上呈现如下内容:

![](img/1f7e13f45b9d9d501795c5fd00b6d076.png)

因此，我们已经授权营销人员使用由我们的工程师开发的组件！

## 翻译

最容易呈现模糊组件的地方是在标记被呈现到 DOM 之后。在 React 中，这是在[*componentidmount*](https://facebook.github.io/react/docs/react-component.html#componentdidmount)*生命周期函数中。对于 FuzzyLeadCapture 组件:*

*上面的代码完成了 4 个独立的任务:*

1.  *查找模糊的 LeadCapture 元素*
2.  *读取它们的标签和按钮文本属性*
3.  *创建 React FuzzyLeadCapture 元素*
4.  *将 React 元素插入 DOM。*

*如您所见，我们只是解析呈现的 DOM，并用 FuzzyLeadCapture React 组件替换现有的 FuzzyLeadCapture 元素。一些开发人员可能会说这有点像黑客，但是它完成了工作。*

*差不多就是这样！你可以想象我们如何扩展这个概念，为营销团队创造更多的交互组件。我们计划构建计算器、比较图表和其他交互式工具来增加我们的营销内容。*

*感谢您的阅读，请查看我们的[网站](https://www.yourfuzzy.com)。*

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*![](img/be0ca55ba73a573dce11effb2ee80d56.png)*