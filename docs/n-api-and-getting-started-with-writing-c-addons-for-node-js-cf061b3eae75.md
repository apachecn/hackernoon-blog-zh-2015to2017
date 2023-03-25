# N-API 并开始为 Node.js 编写 C 插件

> 原文：<https://medium.com/hackernoon/n-api-and-getting-started-with-writing-c-addons-for-node-js-cf061b3eae75>

![](img/69001d9a51ff56fa067a22624447c8d5.png)

Node.js 的最新版本为 Node 世界引入了一些重要的变化和 API 附加功能，但可能最少被提及的附加功能是在 v8.x 中发布的，它离作为 Node 核心的 V8(JS 引擎)越来越远。

> N-API 允许开发人员编写 C/C++插件，而不需要对 V8 的工作有深入的理解，并且提供了更符合 Node 和 Javascript 的抽象。

*更新:由于 N-API 本身仍处于试验阶段，因此本文将根据其重大变化不断更新。当前版本的文章是基于* ***节点 8.6.x 到 8.8.x*** *，对于较早版本的* [*参考本回购*](https://github.com/schahriar/n-api-article) *。*

# 一点历史

C/c++插件长期以来一直是 Node.js 生态系统的一部分，然而开发这些插件一直是一个挑战，即使对于来自 C 世界的开发人员来说也是如此。文档中甚至提到“目前，实现插件的方法相当复杂，涉及到几个组件和 API 的知识”。也许最大的障碍是 V8 的内部，处理 V8 需要很好地理解 C++、JS 以及一些 V8 的内部工作原理。

> *注意 NaN (Node 的节点抽象)是一个很好的抽象层，但只针对 V8。*

问题是，在 Node.js 出现之前，V8 APIs 并没有广泛流行，面向 V8 的消费者只有一个目的，服务 js 并将其快速提供给 Chrome，将 Webkit 和 Chrome 的其他部分与 V8 集成的人是唯一需要深入了解 V8 的人，其他人都跟着一起做。虽然 V8 改变了 web 的景观并触及了后端世界，但它并没有考虑到日常的节点模块，而且就在不久前，微软认为他们的 JS 引擎 Chakra 必须与 V8 一样好或更好才能被广泛采用，所以他们决定废黜 V8 作为 Node 中唯一的虚拟机之王。

Node-ChakraCore 的创意和 addons API 中的简化导致了 N-API 的引入，不知何故它变得令人印象深刻和简单。它允许开发人员编写 C/C++插件，而不需要对 V8 的工作有深入的理解，并提供了更符合节点和 Javascript 的抽象。

# N-API 中的节点模块入门

在这篇文章中，我将介绍这个“实验性”API 的一些伟大的附加功能和好处，供想要开发插件和扩展 Node 范围的 JS 开发人员使用。对于使用 N-API 的普通 JS/C/C++开发人员来说，原生插件世界应该没有那么可怕，因为你不会直接与 V8 打交道。这些抽象中的大部分是用 C 实现的，并且有 C++的语法包装。

## 使用 node-gyp 设置

要构建您的本机模块，您可以利用 node-gyp，gyp 项目是 Chromium 项目使用的另一个构建系统的抽象层，它是构建本机模块的一个很好的工具，并将在适用的地方生成一个 makefile 文件。和大多数 Googly 项目一样，你需要安装 python。然后，您可以从 NPM 安装 node-gyp:

`npm install -g node-gyp`

现在，您可以通过创建一个新文件夹并运行`npm init`来创建一个简单的模块，然后在您的根目录中添加 **binding.gyp** 并为其设置一个目标源。

暂时让模块文件为空，它还不会构建，但是我们很快就会完成。

## NAPI _ 模块和导出的属性

在我们的 C/C++模块文件`module.c`中，我们首先需要包含 NAPI 头文件。这不同于之前可用的`node.h`，而是被命名为 [node_api.h](https://github.com/nodejs/node/blob/master/src/node_api.h) :

```
#include <node_api.h>
```

同样，一个新的函数已经被实现，用于初始化你的节点本机模块，它被称为`NAPI_MODULE`，其行为方式与其前身相似。我们将把模块指向一个返回导出的 Init 方法:

`napi_env`是 V8 隔离或其他虚拟机中的实例的抽象概念，本质上，隔离是 V8 的一个实例，具有自己的堆和垃圾收集器，我们将传递 env 以在同一个虚拟机实例中运行。

是对 JS 变量的一个漂亮的抽象，它代表了一个 JS 值，你可以很容易地创建和填充这些值并将它们返回到 JS-land。

通过在 NAPI 中填充 exports 或 settings module.exports，你可以配置你的插件在 JS 需要时返回一个特定的对象。我们将在 JS 的上下文中创建我们的函数，并使用`napi_create_function`将它分配给一个`napi_value`，使用`napi_set_named_property`将该函数分配给我们的导出对象:

`napi_set_named_property`接受 env，一个对象(在我们的例子中是 exports)，我们属性的名称和它所指向的值，在我们的例子中是我们函数的一个`napi_value`。

您可以使用 define properties 调用将属性分配给代码中的任何 JS 对象，但是这里我们使用 to 来复制类似于:

注意，`napi_set_named_property`的返回类型是一个`napi_status`，N-API 为它的几乎所有调用返回一个状态，这是抛出有意义的错误让 JS 处理的一个非常好的方法。我将在下面的**用 N-API 进行错误处理**一节中介绍更多的错误处理，但是现在你只需要知道如果`status == napi_ok`那么你就没事了。

## 创建我们的 C 函数

现在我们已经在 module.exports 中定义了我们的`MyFunction`函数，作为 descs N-API 方法的一部分，让我们合理地使用我们的函数。它将接受单个 JS 数作为参数，将其转换为 32 位 Int，乘以 2，然后作为另一个 JS 数返回，在我们的例子中，这个 JS 数将被表示为`napi_value`。虽然功能非常简单，但比起“Hello World”，这是一个更好的开始:

我们的函数接受 env/instance 和一个`napi_callback_info`，您可以从中提取参数和其他关于上下文的信息。我们还在函数上定义了 status，作为最佳实践，我们将在多次调用中重用它。

接下来，我们将利用`napi_get_cb_info`来获取作为 N-API 值数组的参数。我们需要定义期望的参数总数或参数计数(argc ),并将最后两个参数`this`和数据指针保留为空:

既然我们已经将 JS 数作为参数向量的第一个成员进行了传递，我们需要将它从 JS 值`napi_value`转换为普通的 int，这样我们就可以在 C 中将其相乘。*因为我们将值从 JS 传递到 C world，所以我们的数据需要一些处理*。我们将调用`napi_get_value_int32`,它也返回一个`napi_status`,然后我们可以用它来验证操作是否成功。我们将向函数传递 env/instance、N-API 值和一个指向我们定义的 int 的指针，以便用结果填充。

我们可以根据 enum `napi_ok`验证我们的状态，并使用接受 env 的`napi_throw_error`调用抛出一个 JS 错误，env 是一个可选的错误代码，我们将它保留为 null，C 字符串 char*作为消息。这样，如果出现任何问题，我们将向 JS-land 抛出一个有意义的错误。

注意，在这种情况下，您可以使用`napi_throw_type_error`来提供更有效的类型错误。

最后，我们将我们的数字乘以 2，并使用`napi_create_int32`将其转换回 JS 值，到现在为止，您可能已经理解了在 N-API 中将 env/instance 作为第一个参数和状态返回的模式。

我们的函数最终看起来会像这样:

一旦你的插件构建完成，你可以通过以下方式从 JS 中调用它，尽管我建议使用[绑定](https://www.npmjs.com/package/bindings)模块:

以下是完整源码:[https://github . com/schahriar/n-API-article/tree/master/Getting _ Started](https://github.com/schahriar/n-api-article/tree/master/Getting_Started)

# 构建和运行你的插件

现在，您拥有了运行程序所需的所有代码，该程序将输出以下事实:

```
8 times 2 equals 16
```

您可以使用以下方式触发构建:

```
node-gyp configure build
```

并使用设置了`--napi-modules`标志的 node 运行您的 JS 文件(仅适用于 8.5.x 及以下版本):

```
node --napi-modules module.js
```

# 所有这些都是为了一个乘以 2 的函数？

你可能会说，用一行代码就可以在 JS 中完成:

但是 C/C++绑定的含义比乘法函数要大得多，乘法函数应该具有与其 JIT 替代函数相同的性能。通过编写原生插件，你可以有效地将 Node 扩展到以前不适合的领域，很好的例子包括 [node-opencl](https://github.com/mikeseven/node-opencl) 或 [leveldown](https://github.com/boingoing/leveldown/) 。

我对原生绑定的第一个实验是实现一组专有的 [GLSL](https://en.wikipedia.org/wiki/OpenGL_Shading_Language) 绑定，这些绑定在 GPU 上渲染一个着色器并返回到一个数组缓冲区，感觉好像我已经突破了 Node 的极限，我鼓励你将 Node 带到从未有过的地方，成为 Node 社区的一部分。