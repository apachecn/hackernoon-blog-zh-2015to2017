# 类固醇上的 Dockerized 开发环境(第二部分)

> 原文：<https://medium.com/hackernoon/dockerized-development-environment-on-steroids-part-2-2-b800a65d0462>

在第 1 部分之后，这里有一个 protip，它将显著提高你的全新 dockerized dev 环境的构建时间。

## 1.怎么，这还不够完美吗？

[在第 1 部分](/@aherve/how-we-happily-dockerized-our-development-environment-part-1-2-b05fd6927a53)中，我们以下面的 Dockerfile 文件结束:

现在让我们看看当你安装一个新的包时会发生什么(在这个例子中，这相当于改变了 *package.json* 文件)。我们将安装“twit”软件包。因为为什么不。如果您理解了第 1 部分，那么您可能已经明白您必须从容器内部运行 *npm install* 命令(因为在我们的计算机上没有 *npm* 这种东西，是吗？):

```
docker-compose run hello npm install --save twit
```

一个 *git 状态*将显示 package.json 已经改变。我们现在必须重建我们的形象

```
docker-compose build
```

这将触发全新安装。

但是有一个问题:npm 安装是从头开始运行的。对于一个大中型项目来说，这确实需要很长时间。而 *gem 也是为 ruby 爱好者安装*的。

此外，CI 服务器构建您的映像也需要很长时间，并且您的测试不会像您希望的那样快。

这个肯定是可以改进的。

## 2.快点！

如果你仔细看看我们的 [Dockerfile](https://hackernoon.com/tagged/dockerfile) ，那么你会看到一切都是以一个 *FROM:mhart/alpine-node* 命令*开始的。*

这基本上意味着我们从一个已经包含 node 和一些基本 unix 工具的 docker 映像开始(尝试一下，您可以运行诸如 *ls、echo、pwd…等等*之类的命令，尽管我们从未专门安装它们)。

= >如果我们构建自己的映像，其中已经包含了一些 *node_modules* 包，会怎么样？

很高兴你问了。让我们这样做:

*   构建一个包含节点+我们当前库的预安装的映像
*   将此映像推送到 docker-hub，这样每个人都可以使用它(如果您使用私有映像，也可以供团队和 CI 服务器使用)
*   在我们 docker 文件的 FROM 指令中使用这个图像
*   利用预安装的库来构建我们当前的项目
*   享受闪电般的构建！

让我们回到正确的方式。

完整的工作目录可以在[这里](https://github.com/aherve/simple-dockerized-dev-env/tree/faster)找到:

```
git clone --branch faster [https://github.com/aherve/simple-dockerized-dev-env](https://github.com/aherve/simple-dockerized-dev-env/tree/faster)
```

首先，让我们准备一个 *base-package.json.* 此时你应该简单地使用你当前的 *package.json* 文件。这意味着这个 package.json 文件下面列出的所有内容都将被安装到您的引导映像中。如果你决定进一步修改，那么只有额外的包需要通过 *docker-compose build* 命令来安装。

这样我们就可以构建我们的引导映像，我们在 *Dockerfile.bootstrap* 文件中描述了它:

既然您已经开始熟悉 docker 文件，您可以看到我们只是简单地使用 base-package.json，并在容器的 */tmp/node_modules* 目录**中安装依赖项。**

此时，您应该构建这个映像，并将其发送到 docker-hub，这样它就可以在*your-log in/my-app-bootstrap*下使用。

出于演示的目的，让我们简单地在本地构建映像，并证明它正在工作:

```
docker build -t bootstrap_demo -f Dockerfile.bootstrap .
```

好了，建好了。我们真的不在乎要花多少时间，我们只打算一劳永逸地建造它。

接下来，让我们修改主 docker 文件，如下所示:

建立我们的全新形象:

```
docker-compose build
```

你有没有注意到 *npm 安装*在这里没有任何关系？绝对比重新安装所有依赖项要快！

有用吗？(剧透:确实如此)

```
docker-compose run test
```

> 计数
> 应返回 1
> 
> 1 次通过(10 毫秒)

现在是最后:让我们更改我们的 package.json，看看会发生什么:

```
docker-compose run hello npm install --save twit
docker-compose build
```

瞧，我们立刻成功安装了 *twit* 包！

您可以检查它是否有效:

```
docker-compose run hello npm list | grep twit
> `-- twit@2.2.3
docker-compose run test
```

> hello 已加载！
> 
> 计数
> 应返回 1
> 1 通过(9 毫秒)

现在，当你触发一个使用你的 docker 文件的全新构建时，只有 *twit* 需要重新安装。

当您厌倦了安装它，只需将它添加到 *base-package.json 中，*构建一个新的引导映像，您用不同的版本标记它，并告诉您的 Dockerfile 使用它。

这实际上只是你日常工作的一个 docker 实现:当你运行 npm install 时，它相当快，**除非**你之前删除了你的 *node_modules。在这种情况下，它不得不从头开始重建一切。*

这个技巧对 ruby 开发人员来说也很好，因为 *package.json* 严格来说等同于 *Gemfile*

## 结论:

我们的教程到此结束。如您所见，使用 docker 构建一个快速、轻量、高效的[开发](https://hackernoon.com/tagged/development)环境并不困难。我们不需要牺牲我们以前拥有的任何特性(比如热重装、自动化测试等),但是除此之外我们还获得了:

*   标准化环境
*   一键安装
*   可在任何计算机上重现的 CI 测试

尽管我们在这里没有谈到生产部署，但是您可以很容易地想象到，将构建容器**从 CI/CD 服务器**推送到某个 docker 托管平台是非常简单的。

在[你可以看看这个 dockered ized typescript-node-express build](https://medium.com/u/4fb3c47e7f1c#.gnqj3w7av)

编码快乐！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！