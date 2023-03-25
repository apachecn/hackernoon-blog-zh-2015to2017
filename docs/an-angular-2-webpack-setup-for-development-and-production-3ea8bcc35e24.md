# 用于开发和生产的 Angular 2 Webpack 设置

> 原文：<https://medium.com/hackernoon/an-angular-2-webpack-setup-for-development-and-production-3ea8bcc35e24>

当处理前端 javascript 项目时，合适的工具可以大大提高你的生产力。当您准备好投入使用时，您需要一个适当的、优化的生产版本。 [Webpack](https://hackernoon.com/tagged/webpack) 可用于两者。配置所有这一切可能是非常艰巨和具有挑战性的。我想给你看一个非常适合我的设置。

![](img/d041ce7f2ee08a78a94d82587d72e751.png)

https://github.com/luukgruijs/angular2-webpack-example:这里有一个 Github 回购:

让我们在应用程序文件夹的根目录下创建一个名为`build`的新文件夹。我们的构建文件夹最终将看起来像这样:

```
- build
  - build.js
  - config.js
  - dev_client.js
  - dev_server.js
  - env_dev.js
  - env_prod.js
  - webpack_base_config.js
  - webpack_dev_config.js
  - webpack_prod_config.js
```

让我们从我们的`config`文件开始，因为这是我们设置的主干。

我们指定了两个配置对象。一个用于我们的生产构建，一个用于我们的开发设置。您可以随意配置这些值。在这个文件中，我们导入两个如下所示的环境文件:

The dev environment file

The prod environment file

现在我们设置我们的`webpack_base_config`文件。这个文件有一些基本的 Webpack 配置，开发和生产版本都会用到。

我们首先指定我们的入口文件。在这种情况下，`vendor`是一个导入所有第三方库的文件，`polyfills`导入 angular 需要的聚合填充，`client`是我们启动应用程序的主要 Angular 文件，通常称为`main.ts`或`root.ts`。最后我们指定了`styles`，它导入了我们所有的。scss 和。css 文件。

之后，我们为 Webpack 在项目中可能遇到的一些文件类型指定加载器。该列表可以根据您的项目需求进行扩展或更改。

然后我们设置一个插件列表。这些插件都将被生产和开发设置使用。您可以使用开发和生产所需的其他插件来扩展插件列表。

# 我们的开发构建设置

我们的开发设置将使用热模块重载。这可以确保您对项目中的文件所做的任何更改都会立即反映在您的页面中。要么直接插入页面，要么自动刷新。

我们的`webpack_dev_config.js`可以这样看:

该配置扩展了`webpack_base_config.js`的配置。该文件首先向每个条目添加热重装中间件。我们在`dev_client.js`中这样做。这个文件可以是这样的:

为了让热模块重新加载发光，我们需要设置一个小的快速服务器。我们将在`dev_server.js`这样做。该文件可能如下所示:

我们现在已经建立了我们的开发构建。我们将在本文末尾添加一个脚本来轻松启动这个构建。我们现在准备好设置我们的生产构建。我们的开发构建设置

# 我们的生产构建设置

我们的生产设置将通过删除所有我们不需要的代码来优化我们的构建，并通过缩小文件和创建 GZIPPED-files 来减小文件大小。我们的生产设置使用`webpack_prod_config.js`文件。该文件可能如下所示:

我们在`build.js`文件中使用这个生产配置。该文件可能如下所示:

`build.js`文件是我们开始生产构建时执行的文件。这个文件启动一个漂亮的微调器，然后清理我们的 dist 文件夹，最后创建新的构建。

# 把所有的放在一起

为了开始开发或生产构建，我们可以在我们的`package.json`中添加脚本。这是我的`package.json`文件，包含脚本和所有依赖项:

如您所见，我们在文件顶部定义了两个脚本。我们可以通过输入`npm run dev`或`npm run prod`来运行脚本。package.json 文件通常位于项目的根目录中。

# 结论

使用此设置，您可以立即开始构建您的角度应用程序。Github 又来了:[https://github.com/luukgruijs/angular2-webpack-example](https://github.com/luukgruijs/angular2-webpack-example)。如果您有任何反馈或关于如何改进的想法，请提出拉式请求。

感谢阅读。有反馈吗？让我知道。也查看我的其他文章:

*   [https://hacker noon . com/global-http-request-error-catching-in-angular-486 a 319 f 59 ab](https://hackernoon.com/global-http-request-error-catching-in-angular-486a319f59ab)
*   [https://medium . com/@ luukgruijs/ngrx-effects-setting-up-reusable-effects-functions-2b 5fa 299577 b](/@luukgruijs/ngrx-effects-setting-up-reusable-effect-functions-2b5fa299577b)
*   [https://hacker noon . com/ngrx-store-reuse-reducer-logic-b6c 0449 e 695d](https://hackernoon.com/ngrx-store-reuse-reducer-logic-b6c0449e695d)

*关注我上* [*中*](/@luukgruijs) *让我们连接上* [*LinkedIn*](https://www.linkedin.com/in/luukgruijs/)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)