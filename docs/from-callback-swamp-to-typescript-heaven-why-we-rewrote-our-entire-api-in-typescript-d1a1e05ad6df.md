# 从回调沼泽到 typescript 天堂:为什么我们用 typescript 重写了整个 api

> 原文：<https://medium.com/hackernoon/from-callback-swamp-to-typescript-heaven-why-we-rewrote-our-entire-api-in-typescript-d1a1e05ad6df>

![](img/3de841831a4aa847bf85170c71b3da6a.png)

Theseus also went back from (callback) hell

在 [Hunteed](https://hunteed.com) ，我们最近重写了整个 api 代码库，从标准的 node-express api 迁移到全新的 typescript 应用。这是一个相当大的挑战，需要相当长的时间，所以当时并不清楚该如何选择。

现在我们已经完成了，我们认为这样做的好处是巨大的。原因如下。

## TL；速度三角形定位法(dead reckoning)

*   它更健壮
*   理解/维护起来更容易、更快
*   代码行减少了 25%
*   不再有回电-见鬼
*   打字稿学习曲线:非常低

# 我们从什么开始(又名*)何必呢？)*

我们从一个 node-express [javascript](https://hackernoon.com/tagged/javascript) 应用开始，最初是用一个流行的 yeoman 版本引导的。

代码是 es6 编写的，一些令人困惑的 babel 配置不知何故让事情变得可行，尽管我不太确定为什么和如何。我们也忽略了大红旗说巴别塔应该*而不是*用于生产(但真的是这样吗？).

应用程序代码覆盖率很好。然而，在实际编写测试之前，测试新特性需要大量的样板文件和复杂的回调。这是一个真正的麻烦，因为只编写未经测试的代码的诱惑只会越来越大。

我开始认为使用大量的 mongoose 钩子使得代码有点难以理解。首先，它看起来很好，很有效率，但是在某些时候，当你调用一个方法时，看不出发生了什么。事情可以更清楚。

很多次试镜。到处都有试镜。当时并不觉得这是个问题，因为我们已经习惯了使用它们。你会看到我们是如何改变想法的:)

# 为什么打字稿？

## 1.打字的明显优势

键入您的代码为您的项目增加了很多健壮性。这一点在其他地方已经讨论了很多，但这里仍然可以提到一些主要优势:

*   *一致性*:有人说如果你的函数有很多参数，那么在某个时候，有人**会**反转它们。调用`f(foo, bar)`而不是`f(bar, foo`现在已经不可能了。Typescript 检查输入/输出，这样您就不会犯这样的错误。
*   *空错误检查*:在 typescript2 中，可以在编译时检查空错误:

*   *I/O 类型检查*:编译器知道你的函数的输入/输出类型。

注意，`[1, 2, 3, 4].map(x => add(x, 1))`将产生一个数字数组`[2, 3, 4, 5]`。编译器能够自己解决这个问题。

## 2.Await 是新的回调

我最喜欢的一个 typescript 特性是类似 ES7 的`async/await`。尽管这是目前 ES7 的一个提议，typescript 已经支持它，并且它知道如何将`await`转换成 ES6 代码(通过利用 ES6 迭代器，但是作为开发人员，您不必担心这一点)。

*【编辑】:* [*从 typescript 2.1 开始，可以将*](http://react-etc.net/entry/async-await-support-for-es5-browsers-pushed-to-typescript-2-1) `[*await*](http://react-etc.net/entry/async-await-support-for-es5-browsers-pushed-to-typescript-2-1)` [*移植到 es 2015*](http://react-etc.net/entry/async-await-support-for-es5-browsers-pushed-to-typescript-2-1)*【o/*

[本帖](https://blogs.msdn.microsoft.com/typescript/2015/11/03/what-about-asyncawait/)涵盖了关于`async/await`的细节。长话短说，只要一个函数被声明为`async,`，它就会返回一个承诺。此外，它将允许使用`await,`，它告诉编译器等待一个承诺来解决，而不阻塞线程。举个例子，

This is non blocking, although it looks like regular synchronous code we all dream of

这让我们可以修改一些代码

our old, tedious, cb syntax

到现在的幻想

new await syntax

结论摆脱繁琐的回调/回调错误处理过程让我们减少了 25%的代码库！

想“许诺”你的一个功能吗？易如反掌:

## 3.奇特的测试

我们过去在测试中有很多回电/承诺。结果，正确初始化一个测试变得越来越困难。

拥有一个乏味的测试环境是一件灾难性的事情。在某些时候，有人会跳过测试，因为这太麻烦了。

有了令人敬畏的[super test-as-promised](https://github.com/WhoopInc/supertest-as-promised)lib 和`async`函数，测试路线现在变得相当优雅:

听着，妈，没有复试！

# 是啊，但是我怎么部署？

和平常一样，除了你应该在上传代码之前先转换它。如果您选择使用`await`，请注意您至少需要转换到 ES6。所以你需要运行一个足够新的版本。大多数现代工具都是这样(比如 heroku、module……)，这对于后端 api 部署来说应该不是问题。

在 [Hunteed](https://hunteed.com) 我们开源了[一个你可以使用的构建](https://github.com/aherve/typescript-express-docker)(查看[这个故事](https://hackernoon.com/edge-node-express-api-using-docker-typescript-codeship-mongoose-37397cd07955#.oxz4ltb63)了解更多细节)。工作流程可以总结为:

*   编写打字稿代码
*   保存文件触发代码转换，在`/dist`中输出新的 js
*   更改 js 文件会触发测试运行。注意，测试实际上是针对传输的 javascript 文件运行的。这很好，因为 transpiled javascript 实际上将在 prod 中运行。当然，映射文件允许将错误链接到您的 typescript 文件。
*   一旦你的`dist`目录被编译和测试，它就是一个很好的旧的 ES6 node-express 应用程序，你可以安全地发送到 prod。显然这是一个 CI-server 作品。你可以，但你不会从你的电脑上做。

## 学习曲线怎么样？

在我看来，对于一个有经验的 javascript 开发人员来说，用 typescript 编写代码只需要不到一天的时间。将你的`function (user) {...}`改为`function (user: User) {...}`已经足够受益于 ts 编译器的强大了。如果你觉得足够勇敢，那么你可以打开`noImplicitAny`编译器选项，这将确保没有变量是未声明的

然而，理解如何从外部库导入类型并不那么明显。有了 typescript 2 就更容易了，因为大多数时候你只需从 npm registery 安装`@types/something`就能让事情正常运行。

不过，总的来说，它非常快，不到一周我们就注意到用 typescript 编码是多么的快速和安全。

## 结论

我们观察到在 typescript 环境中工作的好处:

*   让我改变了对 javascript 的看法。我们现在有了一种看起来不错的编程语言，支持并发。
*   添加测试比以前更容易。
*   现在重构东西非常容易。想要更改函数的签名？只需重新编写，编译器就会告诉你每一行代码应该进行修改。当编译器最终满意时，你也一样，因为它可能会工作。
*   代码更容易理解。函数在它们的定义中是有符号的，接口使得知道任何对象上有什么方法/属性变得非常容易。额外收获:如果你搞砸了，编译器会警告你。

非常感谢每一个为这份令人惊叹的打字稿负责的人。你们统治一切。

![](img/0d54e8100158db49b8f6088a89d90a26.png)

如果您有关于 ts 过渡的反馈，请随时分享！

相关链接:

*   [Edge node-express api 使用 Docker，Typescript，codeship&mongose](https://hackernoon.com/edge-node-express-api-using-docker-typescript-codeship-mongoose-37397cd07955)
*   [我们是如何愉快地将我们的开发环境进行文档化的](https://hackernoon.com/how-we-happily-dockerized-our-development-environment-part-1-2-b05fd6927a53)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)