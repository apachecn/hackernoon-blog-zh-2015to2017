# 使用 Create React 应用程序进行服务器端渲染

> 原文：<https://medium.com/hackernoon/server-side-rendering-with-create-react-app-1faf5a9d1eff>

更新:我为此写了一个 v2，它的推理更好一些，在这里查看:[https://medium . com/@鲁本/SSR-with-create-react-app-v2-1b 8 b 520681d 9](/@benlu/ssr-with-create-react-app-v2-1b8b520681d9)

我一直在使用 create react 应用程序，并希望通过在我制作的应用程序中添加服务器端渲染来提高性能。这并不像我最初想的那样简单，但要开始，您可以查看这篇 medium 文章:[https://medium . com/@ patriciolpezjuri/using-create-react-app-with-react-router-express-js-8fa 658 BF 892d](/@patriciolpezjuri/using-create-react-app-with-react-router-express-js-8fa658bf892d#.4uaa27t0d)

> 这是关于服务器端渲染和 redux

源代码可用:【https://github.com/ayroblu/ssr-create-react-app 

# 借口

在生产中，create react app 基本上只是一些静态文件，这对于基本部署来说很好，但合并服务器端渲染需要服务器，我已经使用了 nodejs 相当多，所以遵循一点[https://medium . com/@ patriciolpezjuri/using-create-react-app-with-react-router-express-js-8fa 658 BF 892d](/@patriciolpezjuri/using-create-react-app-with-react-router-express-js-8fa658bf892d#.4uaa27t0d)，您可以设置一个 express + create react app 网站，并开始添加服务器端渲染。

我所做的并不涉及会话状态修改或服务工作者，我认为这两者都是自然的进展，但只是还没有实现。

# 理论

对于服务器端渲染，我们希望渲染一些基本的页面，我不想在认证用户或复杂的条件下乱来，因为我通常在客户端以单页应用程序(SPA)的方式处理这些。这很大程度上意味着我想呈现原始的 html，而不是别的，让 html 保持原样，但是用路由信息填充 react 'root '元素

# 做出改变

## 应用程序设置

对于那些不熟悉 redux、react-router 设置或好奇我是如何做到的人来说:我一直在尝试不同的 redux 风格，所以目前我有三个文件夹，动作、reducers 和类型，看起来像:

store.js 看起来像:

显然，基于我相信的 react 样板文件，我坚持在我觉得合适的地方(redux-saga、logger、dev tools……)居中

对于您的路线，将它们移动到它们自己的组件 routes.js 中，如下所示:

显然，我也有一个组件和容器目录，容器是主页面，组件是没有状态的东西，实际上只是提供简单的功能，似乎是事情进展的方式

## 现在实际的服务器端渲染

好了，有了样板文件，如果您已经按照链接的文章进行了 express server 设置，那么您现在已经得到了一个 *server/app.js* 文件。首先，有几个重要的导入:

这些将你的客户端 react 代码带到 nodejs 端，(如果你还没有使用 *npm install/yarn add* ，不要忘记安装 ignore-styles)。我们忽略-styles，因为你在 create react app 中导入 css，没有 webpack，我们真的不能处理，所以这是一个快速的欺骗。babel-register 对于生产来说显然不是一个好主意，但是，在很大程度上，它很好，而且你需要它，因为你已经将你的客户端代码 babel 化了。剩下的就不言自明了，你需要创建你的 react 元素并把它们转换成字符串。

注意:因为我们在路由器级别工作，如果你有父组件在那之上，在这种情况下，redux，但是也许 material-ui 或类似的，那么你将需要在这里重建它们(或者需要文件以便你能使用 import 等)

下面是代码，它并不漂亮，但它完成了工作:

所以快速浏览一下，这个函数放在一个快速处理程序中，你首先读取 create-react-app 生成的文件(index.html 在构建目录中)。使用 react-router 的匹配方法来匹配 url，并给定一些道具，将我的 react 元素创建为 html。最后替换一些文本(因此进入 public/index.html 并向根元素添加一些内容，如{{SSR}}，然后数据被替换并最终发送给用户。

你完了！不要忘记一些零碎的东西，比如:

```
app.use('^/$', universalLoader)
app.use(express.static(path.resolve(__dirname, '..', 'build')))
app.use('/', universalLoader)
```

对于更高级的项目，比如那些已经退出了 webpack 配置的人，这可能还不够，但是如果你正在创建一些简单的网站，这是一个加快加载速度的好方法！

https://github.com/ayroblu/ssr-create-react-app[的 check out](https://github.com/ayroblu/ssr-create-react-app)这是我在这里使用的样板代码(这意味着这里有图标，代码更加分散)。让我知道你的想法，肯定要增加服务人员，除了其他事情！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！