# 牛仔 1.3 的亮点

> 原文：<https://medium.com/hackernoon/highlights-of-buckaroo-1-3-adf74b1b4aea>

[牛仔](http://buckaroo.pm/) 1.3 已经[发布](https://github.com/LoopPerfect/buckaroo)！这是我们一直在做的事情的明细…

# Debian 的简化安装🐧

我们现在为 Buckaroo 和 Buck 托管了 [Debian](https://github.com/LoopPerfect/buckaroo/releases/tag/v1.3.0) 包，以简化安装过程。此外，我们为 linux-amd64 提供了一个独立的 Buckaroo 版本，不需要安装 java

# GitHub、GitLab 和 BitBucket 集成🐙🐈

我们坚信去中心化，并希望鼓励开源，所以现在你可以直接从 GitHub、GitLab 和 BitBucket 安装软件包。您所要做的就是在安装时指定软件包源。例如:

`buckaroo install github+loopperfect/neither`

我们通过 SSH 连接，所以也支持私有存储库！

# 支持 4 位数版本号🍀

一些库，比如 GLM 的版本号中有第四个数字。虽然这不是严格意义上的[语义版本](http://semver.org/)，但是很容易扩展语义版本化方案来支持它们。我们认为这是一个实用的解决方案，所以我们将它添加到 Buckaroo 中。

# 准备好快速入门了吗？🏃‍♀️

前往[文档](https://buckaroo.readthedocs.io/en/latest/)开始。

![](img/d9c51d1ab95f67e9247e02a9ac24a1b7.png)

# 为什么是牛仔？🤔

[](https://hackernoon.com/approaches-to-c-dependency-management-or-why-we-built-buckaroo-26049d4646e7) [## C++依赖管理的方法，或者我们为什么要建立 Buckaroo

### C++是一种不寻常的语言，因为它还没有一个占主导地位的包管理器(我们正在努力！).结果是…

hackernoon.com](https://hackernoon.com/approaches-to-c-dependency-management-or-why-we-built-buckaroo-26049d4646e7) [](https://hackernoon.com/6-reasons-why-we-distribute-c-libraries-as-source-code-2dc614d5ef1e) [## 我们将 C++库作为源代码发布的 6 个原因

### 编写 C++应用程序时，不可避免地会用到外部库。这是好事！代码重用…

hackernoon.com](https://hackernoon.com/6-reasons-why-we-distribute-c-libraries-as-source-code-2dc614d5ef1e)  [## 使用降压构建的 7 个理由

### Buck 是一个跨平台、跨语言构建系统，在脸书为大规模编译而设计。所有牛仔…

hackernoon.com](https://hackernoon.com/7-reasons-to-use-buck-build-5b44d7413585)