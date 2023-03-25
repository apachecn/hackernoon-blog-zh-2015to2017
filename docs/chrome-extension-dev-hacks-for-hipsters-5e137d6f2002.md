# 为潮人开发的 Chrome 扩展

> 原文：<https://medium.com/hackernoon/chrome-extension-dev-hacks-for-hipsters-5e137d6f2002>

![](img/d882b3cd2c7083a2ef77b817e0d2fbae.png)

No hipsters were harmed in the making of this article

继[为潮人准备的 Javascript 技巧](http://berzniz.com/post/68001735765/javascript-hacks-for-hipsters)博客帖子成功之后，这里是另一个有用的提示。这次是为那些正在开发 Chrome 扩展的人准备的。

因此，按照绝对顺序，它们是:

**#1。弹出开发**。使用弹出窗口是非常令人沮丧的——虽然使用开发工具检查弹出窗口应该会使其保持打开，但情况并非总是如此。此外，您不能在工作时浏览其他网站(stackoverflow 任何人？).

诀窍是通过使用弹出窗口的 URL 在它自己的标签中打开**弹出窗口。您可以通过检查弹出窗口并在控制台中使用以下命令来找到它`location.href`——URL 的形式为`chrome-extension://<your_extension_id>/<your_popup>.html`。然后你就可以同时使用扩展和 Chrome 开发工具了。**

**#2。热重装**。如果使用 React，您可以使用热重装特性来使开发快速而连续。我使用下面的[样板文件](https://github.com/samuelsimoes/chrome-extension-webpack-boilerplate/tree/react)开始，它为 React 和 HMR 配置了 webpack。

**#3。分析**。不要忘记发送包含您的分析事件的扩展版本。你可以用

`const version = chrome.runtime.getManifest().version`

**#4。造型**。对于内容脚本来说，使用`CSS in JS`解决方案是非常棒的。内容脚本被注入到网页中，像`[glamor](https://github.com/threepointone/glamor)`这样的库消除了考虑 CSS 注入的需要。只需编写代码，其余的由`glamor`完成。

**#5。自动版本控制。每个新版本的扩展都需要一个新的版本号。虽然您可以手动修改扩展版本并提交到您的源代码控制中，但是让您的构建脚本来做要容易得多。你可以用 npm 模块`[chrome-web-store-item-property](https://www.npmjs.com/package/chrome-web-store-item-property)`查询你的扩展的 Chrome 网络商店版本，然后增加版本并发布到商店。**

**#6。承诺 API** 。`chrome`对象公开了一个基于回调的 API，效果很好，但是`Promise`更容易使用。虽然你可以自己包装每一个功能，`[chrome-promise](https://github.com/tfoxy/chrome-promise)`已经为你做了。这也将允许您利用`async/await`语法。

**#7。使用最新的浏览器功能**。由于你运行在 Chrome 上，你可以使用所有最新的 API(如`fetch`、`async/await`、`position: sticky`)，编写更少的代码，也减少了编译时间。如果没有翻译步骤，调试也更容易(源地图也是一种解决方案，但是调试实际的源代码总是更好)。

**#8。多个目标。**大多数项目至少有 3 个不同的目标，一个用于开发，一个用于试运行，一个用于生产。我保存了 3 个不同的`manifest.json`文件，构建过程捆绑了正确的一个。然后所有 3 个扩展完美地并排运行。

**#9。内部分机共享。**使用一个共享的 Dropbox 文件夹，将扩展复制到那里，这样整个公司都可以从那里加载它(他们需要将它作为一个未打包的扩展来加载)。更新比发送到 Chrome 网络商店要快得多。

请在评论中分享你开发 Chrome 扩展的技巧和诀窍。

> 如果你喜欢这篇文章，点击下面的❤按钮

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)