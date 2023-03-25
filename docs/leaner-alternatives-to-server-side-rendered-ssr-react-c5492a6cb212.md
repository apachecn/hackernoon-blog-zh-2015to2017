# 服务器端渲染(SSR)的替代方法 React

> 原文：<https://medium.com/hackernoon/leaner-alternatives-to-server-side-rendered-ssr-react-c5492a6cb212>

# **更新:**

我从[谷歌](https://hackernoon.com/tagged/google)找到了这个[工具](https://hackernoon.com/tagged/tool)来帮助我们进行搜索引擎优化测试:

[https://support.google.com/webmasters/answer/6066468?visit _ id = 1-636360191230083689-3793429009&rd = 2](https://support.google.com/webmasters/answer/6066468?visit_id=1-636360191230083689-3793429009&rd=2)

*“抓取为谷歌工具使您能够测试谷歌如何抓取或呈现您的网站上的网址。您可以使用 Fetch as Google 来查看 Googlebot 是否可以访问您网站上的页面，它如何呈现页面，以及是否有任何页面资源(如图像或脚本)被 Googlebot 阻止(通过 robots.txt)。此工具* [*模拟爬网*](http://www.google.com/insidesearch/howsearchworks/crawling-indexing.html) *并在 Google 的正常爬网和呈现过程中呈现执行，对于调试您网站上的爬网问题非常有用。*

我在这里建议的流程是**在**进入解决方案模式之前，我们必须首先了解谷歌如何抓取客户端渲染的 SPA(没有同构或服务器端模板，只是一个纯客户端应用程序)，如果谷歌无法抓取它(谷歌自 2009 年以来一直投资于他们抓取 SPA 的能力，到 2014 年，他们宣布他们能够抓取几乎任何 SPA)，那么我们将使用 Headless Chrome 方法。

# **更新帖子:**

首先，我想澄清一下，这篇文章并不是关于静态(构建时)服务器端渲染(SSR)的，在 SSR 中，服务器是一个独占的资源，正如像 [Gatsby 这样的框架所提倡的那样。JS](https://github.com/gatsbyjs/gatsby) 。它是关于动态(运行时)SSR 的，其中“服务器”是一种共享资源，就像像 [Next 这样的框架所提倡的那样。JS](https://github.com/zeit/next.js/) (但不包括接下来的[的组合。JS 和 electronic](https://leo.im/2017/electron-next)服务器再次成为独占资源。)

让我们从我的简单用例开始。我有一个反应应用程序，我希望暴露给搜索机器人(搜索引擎优化)，并加快它的初始渲染。我不相信使用 SSR，因为它否定了我们通过单页面应用程序(SPAs)获得的可伸缩性，在单页面应用程序中，我们在客户端上完成渲染页面的所有 CPU 受限工作，而不是在共享服务器上完成，在共享服务器上，最常见的场景涉及作为服务器的 NodeJS，这是一个单线程、协作的多任务环境，专为 I/O 受限工作而设计。

我考虑过的一个选择如下:

在接收到对给定 URL 的请求时，服务器将 index.html 文件加载到字符串中，并注入到初始请求路由的数据中(必须在 URL 参数中设置(由用户和 SPA ),因为浏览器不会转发散列片段，并且包括任何查询参数),如下所示:

1.  所有 JSON 数据和文本都作为 JS var 声明注入到脚本标签中，例如 ，它可以被位于服务器修改的 index.html 底部的 index.js 脚本(SPA)访问。
2.  相应的 HTML 元标签被注入头部。

通过这种方式，当浏览器加载 SPA 时，初始路径将已经具有它需要的数据，并且将在客户端上开始渲染，而不必等待数据，因此初始渲染将感觉很快，因为浏览器不会打开空白页，然后等待再渲染。相反，它会打开一个空白页，然后立刻呈现所有内容，所以用户甚至看不到以零零碎碎的方式组合起来的空白页。在最初请求的路由被提供后，SPA 接管并可以确保后续路由仅在它们的数据依赖项被获取后才被呈现，从而达到相同的效果。

至于我们将如何在服务器上预取数据，我们将使用 GraphQL 作为数据层，这允许我们使用相同的 GraphQL 模式和解析器来执行每个路由的单个查询，而不必仅为了数据预加载而为每个路由构建命令性 reducer 逻辑。

说到 SEO(见本文顶部的更新),我见过的最好的方法是 Google Chrome 团队的 Sam Li。山姆的[方法](/dev-channel/solving-seo-with-headless-chrome-for-your-client-side-framework-288e66fdd2b7)是检测用户代理，如果它是一个机器人，然后将请求排队到一个无头 Chrome 实例池，它将像任何用户浏览器一样加载 SPA，运行它并返回 HTML。优点是我的 React 应用程序设置仍然非常简单，没有任何 SSR 包袱。

加速初始渲染的另一种方法:

至于反作用应用程序膨胀问题，即开发人员堆积 NPM 模块的负载，并且不使用死代码消除或树抖动(学习两者之间的[差异](/@Rich_Harris/tree-shaking-versus-dead-code-elimination-d3765df85c80))，一个解决方案，正如服务工作器和代码拆分中的[Kayle Mathews](https://twitter.com/kylemathews)创建者所建议的，是使用缓存功能(因为在本文写作时，我们还没有完全工作的[动态导入](https://bugs.chromium.org/p/v8/issues/detail?id=5785))来缓存我们的代码所依赖的所有供应商模块，并单独加载它们，这样我们就不会下载兆字节的依赖项

**加速初始页面渲染的另一种方式**(在专家系统提议阶段，所以它可能会实现，也可能不会实现)是这个关于 JS 无线格式的提议，作为抽象语法树的二进制编码:

[](https://github.com/syg/ecmascript-binary-ast/) [## tc39/建议书-二进制-ast

### ECMAScript 的二进制 AST 提案。通过在…上创建帐户，为 tc39/建议书-二进制-ast 开发做出贡献

github.com](https://github.com/syg/ecmascript-binary-ast/)