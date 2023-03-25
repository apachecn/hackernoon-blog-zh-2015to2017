# 免费的 Node.js 建立在谷歌云功能之上

> 原文：<https://medium.com/hackernoon/free-node-js-builds-on-google-cloud-functions-6e305623e249>

tldr: [源代码和部署指令](https://github.com/jchorl/buildserverless)

# **动机**

如果你在软件公司工作过，你可能对构建服务器很熟悉。[使用它们有很多理由。](http://stackoverflow.com/a/1099312)当代码被构建并合并到产品中时，通常是在一个标准化的环境中，在称为构建服务器的专用服务器上完成的(想想像 Jenkins、CircleCI 等工具)。

最近， [AWS Lambda](https://aws.amazon.com/lambda/) 和 [Google Cloud Functions](https://cloud.google.com/functions/) 越来越受欢迎，因为它们允许开发人员更多地考虑代码，更少地考虑服务器，同时严格支付他们消耗的内存和时间。谷歌最近宣布为他们的大多数云产品提供非常慷慨的免费服务，包括每月使用大量云功能。

由于云函数在 Node.js 环境中执行代码，应该可以构建在 Node.js 环境中构建的应用程序(想想所有的 [Webpack](https://webpack.github.io/) 前端，比如 React 和 Angular 应用程序等等)。所以我决定构建一个云函数，可以构建 Node.js 内置的前端。

# 它是如何工作的

云功能是通过 HTTP 调用触发的(想想: [Github webhooks](https://help.github.com/articles/about-webhooks/) )。概括地说，步骤非常简单:

1.  **下载源代码** —简单地克隆一个 Github repo。
2.  **运行构建** —执行您将在命令行上执行的构建命令，如 *npm 运行构建。*
3.  **打包构建结果**——因为构建可能输出多个文件，所以最简单的方法是将所有这些结果打包成一个 tarball 并压缩，这样就可以将它推到其他地方了…
4.  **推送结果** —云函数在临时环境中执行，因此构建结果必须被推送到某个持久的地方，以便以后使用。对它们进行打包和压缩使这变得更容易，并节省了网络出口。我选择将打包的 build 推送到 [GCS](https://cloud.google.com/storage/) ，但是真的可以推送到任何地方。

# 数字

我测试构建了一个由 [Create React App](https://github.com/facebookincubator/create-react-app) 创建的基础应用。我只能让它在 2GB 内存、2.4 GHz 配置下运行。每次跑步不到 2 分钟。因此，根据[Google Cloud Functions always free limits](https://cloud.google.com/free/docs/always-free-usage-limits)，CPU 资源早在 RAM 之前就用完了。

200000 免费 GHz-秒/(每次运行 2.4 GHz * 120 秒)= ~ **694**

这意味着每个月在谷歌云功能上免费运行~694 个前端构建是很有可能的。鉴于云功能的定价模型，我不确定有多少构建值得运行专用的构建服务器。

# 云功能面临的挑战

这是我第一次在云功能上构建任何东西，一般来说是无服务器的。鉴于云功能还处于测试阶段，我相信它会继续以很好的方式发展。以下是我在构建这个项目时遇到的一些最大的挑战:

**没有文件系统**

不幸的是，云函数并不能真正让你访问文件系统。他们确实给了你访问 [*/tmp*](https://cloud.google.com/functions/pricing#local_disk) 的权限，但是这似乎是针对你分配的 RAM 的，RAM 最大容量为 2GB。当从 Github 下载压缩的 tarball、解压缩、解包、写入磁盘以及安装该项目的所有 Node.js 构建依赖项时，这是一个问题。我不确定这实际上需要多少内存，但天真地说，它可以达到代码库大小的 4 倍以上。

**仿真器与生产**

[云函数模拟器](https://github.com/GoogleCloudPlatform/cloud-functions-emulator)和云函数生产环境之间存在一些差异，这使得开发变得困难。虽然模拟器是由谷歌开发的，但他们给出了一个免责声明，说明它不是谷歌的官方产品，所以这是公平的。

一个例子是使用 Node.js' [子进程](https://nodejs.org/api/child_process.html)执行命令。由于模拟器似乎并不运行在一个孤立的环境中，依赖于$PATH 中的可执行文件可以在模拟器上工作，但不能在生产环境中工作。

我最终使用 [Github Contents API](https://developer.github.com/v3/repos/contents/#get-archive-link) 模仿了 *git clone* ,因为我无法让最上面的几个 Node.js git 包在产品上工作，即使它们在模拟器上工作。常见的问题包括需要安装基本的加密库和 git。

**部署缓慢**

部署总是需要几分钟，即使是部署简单的云功能。这使得调试仿真器与生产问题非常耗时，因为我必须为每一个小的变化重新部署(我有很多这样的变化)。

**没有 Node.js 可执行文件**

能够构建 Node.js 应用程序需要访问节点*节点*和 *npm* 可执行文件。鉴于云函数是在 Node.js 环境中执行的，我认为执行节点代码是微不足道的——我错了(有点)。虽然我可以在模拟器中*执行 Sync* node 命令，但是产品找不到 *node* 或 *npm* 可执行文件。我的第一个行动是查看我是否有任何可执行文件——是的，有效。echo＄PATH——似乎＄PATH 中有几个目录——很好。*find/-name node-executable*—由于某种原因这没有返回任何东西(不确定为什么)，但是我有其他想法。我知道 C 允许你通过 *argv* 看到调用命令，结果[节点也能看到](http://stackoverflow.com/questions/4351521/how-do-i-pass-command-line-arguments)。太棒了，我找到了*节点*可执行文件(/nodejs/bin/node)！我[列出了包含目录](http://stackoverflow.com/questions/2727167/how-do-you-get-a-list-of-the-names-of-all-files-present-in-a-directory-in-node-j)的内容，果然 *npm* 也在里面。我将该目录添加到$PATH 中，然后又回到了正轨。

# 未来的想法

1.  正在构建的项目的缓存 node_modules。每个构建当前都运行 *npm 安装*，这花费了大约 60%的构建时间。缩短这个时间会大大降低每次构建的成本。
2.  从[节点 tar](https://github.com/npm/node-tar) 切换到 [tar-fs](https://github.com/mafintosh/tar-fs) 。tar-fs 显然比 node-tar 快 10 倍，但不幸的是需要 *chmod* 。默认情况下，云函数上的 chmod 不在$PATH 中，所以我假设它不可用。让 tar-fs 在未来工作是件好事。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！