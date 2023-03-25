# Buckaroo 1.2 有什么新功能？

> 原文：<https://medium.com/hackernoon/whats-new-in-buckaroo-1-2-8844eb6bbf5d>

Buckaroo 版本 1.2.0 已经发布，它带来了许多新功能。你可以[从现在开始获取最新版本](http://buckaroo.readthedocs.io/en/latest/installation.html)，或者如果你只是好奇，这里有一个新功能的分解。

## 等等，牛仔是什么？

[Buckaroo](http://buckaroo.pm/) 是 C/C++的依赖管理器。与其他解决方案不同，Buckaroo 专注于快速、可重复的跨平台构建。也是死的——用起来简单；如果你熟悉 NPM 或纱，那么你应该有宾至如归的感觉！

## GitHub 依赖项🌍

现在把你的 GitHub 项目变成牛仔食谱已经很简单了。这些食谱是直接在 GitHub 上创建和管理的，所以你不需要运行服务器或者等待批准来创建一个包！😌

只需将一个`buckaroo.json`文件添加到项目的根级别，并在 GitHub 网站上创建一个 release 标签。Buckaroo 将在解决依赖性时智能地爬取存储库。

参见:[为 GitHub 项目添加 Buckaroo 支持](http://buckaroo.readthedocs.io/en/latest/github-package-guide.html)

## 依赖关系锁🔒

Buckaroo 现在将解析的依赖项保存到一个锁文件中，该文件位于您的项目中。当在多台机器上构建时，这是一个更好的方法，因为它确保所有的依赖信息都包含在项目的源代码中。

如果你的项目已经在使用 Buckaroo，运行`buckaroo install`来创建一个锁文件。

## 异步执行⏱️

我们做了大量的工作来支持 Buckaroo 内部任务的异步执行。现在，下载、依赖关系解析和安装都是并行进行的，因此您可以快速地重新开始编写代码。

## 反应式用户界面⚡

新的控制台 UI 支持 ANSI，在执行任务时提供丰富的输出。

![](img/e0e97809d3b4ed7857d8c63c7ffd4e9d.png)

Reactive Console UI

实现挺有意思的，借鉴了 React 和 Cycle.js 的思路[看一下](https://github.com/LoopPerfect/buckaroo/tree/master/src/main/java/com/loopperfect/buckaroo/virtualterminal)。

## 更好的缓存📦

我们已经改进了缓存策略，以便在项目之间共享缓存。这确保了你永远不会下载一个依赖项两次，也使得 GitHub 集成很快。

## 更好的错误消息🐛

错误信息有了很大的改进，现在 Buckaroo 会在很多情况下建议解决错误的措施。

![](img/e5301bcbd4dd413b0ce481afa5a2220f.png)

Informative Error Messages

## Debian 包装🐧

你们中的一些人不喜欢在主安装过程中使用 Linuxbrew，所以现在我们也提供了 Debian 包。前往[文档](https://buckaroo.readthedocs.io/en/latest/installation.html#linux)开始。

# 试试牛仔

就是这样！🙌如果你想尝试牛仔，最好从[文档](https://buckaroo.readthedocs.io/en/latest/)开始。你可以[在 Buckaroo.pm](https://buckaroo.pm/) 上浏览现有套餐，或者在[愿望清单](https://github.com/LoopPerfect/buckaroo-wishlist)上请求更多套餐。

![](img/ce2bbe9eccda380d85cb7a18167d620f.png)

“Hello world” with Buck and Buckaroo