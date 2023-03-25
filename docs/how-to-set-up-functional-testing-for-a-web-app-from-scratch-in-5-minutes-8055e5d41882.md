# 如何在 5 分钟内为一个 web 应用从头开始设置功能测试

> 原文：<https://medium.com/hackernoon/how-to-set-up-functional-testing-for-a-web-app-from-scratch-in-5-minutes-8055e5d41882>

假设你有一台干净的 PC，没有任何开发工具。你能多快安装好所有需要的测试工具，编写并运行你的第一个 web 功能测试？一个小时？还是两个？

我们认为 5 分钟已经足够了。说真的。

启动您的计时器，并遵循以下步骤。

# 我需要什么？

Windows (Mac 或 Linux)电脑和互联网连接。

# 节点安装

在你的电脑上安装 [Node.js](https://hackernoon.com/tagged/nodejs) 。如果你运气不好已经安装了，谷歌“node.js download”，打开页面，下载最新版本并安装。最懒的一个[链接](https://nodejs.org/en/download/)。

![](img/a99c4fe4df678d62df531f7f34bda7d7.png)

# TestCafe 安装

现在，您的机器可以安装 TestCafe 了。您可以从 Node.js 包管理器( [npm](https://www.npmjs.com/) )中用一个命令来完成它。打开控制台，键入以下文本，然后按 Enter 键:

```
npm install testcafe -g
```

![](img/c7d97168ae4a0ae0dec1509bee4d2c56.png)

# 测试写作

让我们写一个测试。你所需要的是你最喜欢的文本编辑器。在下面的例子中，我们将检查谷歌在搜索“GitHub 上的 TestCafe”时是否先对 TestCafe GitHub 知识库进行排名。

用以下代码在`D:\test`中创建一个`test.js`文件:

这里绝对没有魔法。

![](img/169cdb2099476cb4efdde3e2a44b935e.png)

# 试运转

开始测试的一切准备就绪:

在控制台中进入`D:\test`目录，写一个命令打开浏览器并开始测试:

```
testcafe chrome test.js
```

等待测试成功完成，并研究测试结果。

![](img/20c8f388e370bcb1360e0d52aaf71cd1.png)![](img/62fe4902288b5711d5ba3c89751b9e4d.png)

当然，根据机器性能和互联网连接速度的不同，每个步骤花费的时间也可能不同。
然而，我们已经在 **4 分 45 秒**内完成了本教程。而且我们还有额外的 15 秒:)

# 你还有 5 分钟吗？

您可以用另外三种方式运行此测试:

*   在你电脑上安装的任何浏览器中，不要写“chrome”，而要写“firefox”、“edge”等。`testcafe firefox test.js`；
*   在本地网络中任何机器上的远程浏览器中——而不是“chrome ”,写“remote ”,并使用远程机器打开一个将显示的 URL`testcafe remote test.js`；
*   在本地网络中的任何移动设备上，只需扫描二维码([见视频](https://twitter.com/DXTestCafe/status/804368394569052160) ) `testcafe remote test.js — qr-code`。

所以，你花了不到 5 分钟来编写你的第一个测试。而这仅仅是开始，因为 TestCafe 还有更多有用的功能让你大吃一惊。

*   [TestCafe 网站](https://devexpress.github.io/testcafe/)
*   [GitHub 上的 test cafe](https://github.com/DevExpress/testcafe)
*   [TestCafe 文档](https://devexpress.github.io/testcafe/documentation/getting-started/)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)