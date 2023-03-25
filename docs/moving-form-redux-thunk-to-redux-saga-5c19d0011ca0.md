# 从 redux-thunk 到 redux-saga

> 原文：<https://medium.com/hackernoon/moving-form-redux-thunk-to-redux-saga-5c19d0011ca0>

我当时正在做一个 ***React，Redux*** 项目，这个项目涉及很多 REST API 调用，我决定使用 [redux-thunk](https://github.com/gaearon/redux-thunk) 来处理这个项目。毫无疑问，这个库真的很棒，但是有很多多余的代码，所有这些调度和所有这些真的增加了项目的规模，并且随着复杂性的增加，它真的变得很难处理和管理

我的项目正在快速发展，我可以看到随着越来越多的使用 [redux-thunk](https://github.com/gaearon/redux-thunk) ，代码变得越来越冗余。所以我决定在这个周末尝试 redux-saga，我对结果感到非常惊讶。

首先，要理解 [redux-saga](https://github.com/redux-saga/redux-saga) 你需要理解 ***生成器*** 如何在 Javascript 中工作。我之前在 python 中使用过它们，所以我知道它们是如何工作的，但是如果你对生成器没有任何概念，从这里的[开始做](https://davidwalsh.name/es6-generators)然后继续。如果对发电机没有很好的理解，你就不会明白 [redux-saga](https://github.com/redux-saga/redux-saga) 是如何工作的。

为了比较 redux-thunk 和 redux-saga，我决定创建一个小项目，该项目从远程 API 获取数据，更新本地存储，我的组件从该存储中读取数据并在页面上显示出来。作为《星球大战》的粉丝，我使用《星球大战》的开放 API 作为终点。

现在来看这个项目，我们需要为 redux-thunk 和 redux-saga 创建动作、reducers 和 store。所以就配置而言，几乎所有代码都是一样的。redux-saga 中的动作部分变得非常简单，因为生成器会让冗余的调度代码变得过时。

以下是来自 redux-thunk 和 redux-saga 的操作:-

现在，如果你看到我们能够将承诺传递给 redux-saga 中间件，它会自动处理它。这是我所期待的，因为它不涉及调度，代码冗余度大幅下降。

您可以通过以下链接查看 redux-thunk 和 redux-saga 的代码

1.  redux-thunk—[https://github . com/Jin xac/redux-thunk-saga/tree/ba 7 e 60 bdfe 1 a5 e 689 de 117 ed 095782 EC 22 ff 322d](https://github.com/jinxac/redux-thunk-saga/tree/ba7e60bdfe1a5e689de117ed095782ec22ff322d)
2.  redux-saga—[https://github . com/Jin xac/redux-thunk-saga/tree/6d 85 f 28 a 41 af F2 ce f1 c 9 af 6 da 259 b 88962 a 082 bb 5](https://github.com/jinxac/redux-thunk-saga/tree/6d85f28a41af2cef1c9af6da259b88962a082bb5)

我真的相信，如果您的项目即将扩展或者已经达到那个水平，redux-saga 是处理 react 项目中 REST 调用的更好方式。我计划将我的项目从 redux-thunk 转移到 redux-saga。

如果你喜欢这篇文章，你可以在 [github](https://github.com/jinxac) / [twitter](https://twitter.com/salujaharkirat) 关注我。