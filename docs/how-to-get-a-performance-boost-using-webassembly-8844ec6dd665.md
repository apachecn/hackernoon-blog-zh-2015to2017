# 如何使用 WebAssembly 提升性能

> 原文：<https://medium.com/hackernoon/how-to-get-a-performance-boost-using-webassembly-8844ec6dd665>

![](img/fa05443bbab2fd2bf0131a5126710388.png)

新的一年刚刚开始，随之而来的是新的决心要实现。学习如何使用 WebAssembly 并获得性能提升怎么样？

本文是我们正在撰写关于性能的系列文章的继续，请查看“[如何使用 Node.js 原生插件获得性能提升”](/developers-writing/how-to-get-a-performance-boost-using-node-js-native-addons-fd3a24719c85)和“[通过 Ruby time 解析优化获得 1300%的性能提升！](/@devlucky/ruby-time-parsing-optimization-524622354201)“✌️

今天我想向您演示如何创建和使用 WebAssembly 模块，并在以后从浏览器中使用它们，就像它们是 js 模块一样。为了做到这一点，我将采用斐波那契算法，我[已经在这里讨论过了](/developers-writing/fibonacci-sequence-algorithm-in-javascript-b253dc7e320e)，我将测试它作为普通 javascript 函数和 WebAssembly 模块运行时的性能。

# 履行

我们将讨论前一篇文章中已经讨论过的 3 种技术:

*   环
*   递归
*   记忆化

以下代码片段涵盖了 Javascript 和 c 语言中的这些实现。

> java 描述语言

> C

我不会解释这些函数是如何工作的，因为这篇文章不是关于这个的。如果你想了解更多，请查看[这个](https://en.wikipedia.org/wiki/Fibonacci_number)或 T10 这个。

# 一点历史

*   **WebAssembly** 诞生的前提是创建一个安全、可移植、快速加载和执行的适合 web 的格式。WebAssembly 不是一种编程语言，它是一个既有文本规范又有二进制规范的**编译目标**。这意味着像 C/C++、Rust、Swift 等低级语言。可以用 WebAssembly 输出进行编译。它与 javascript 共享堆栈，这就是它不同于 java 小程序的原因。此外，它的设计是一个社区的努力，所有的浏览器供应商都在努力。
*   **Emscripten** 是一个 LLVM-to-JavaScript 编译器。这意味着像 C/C++这样的语言或者任何使用 LLVM 的语言都可以被编译成 JavaScript。它提供了一组 API 来将你的代码移植到网络上，这个项目已经运行了很多年，通常用于将游戏移植到浏览器上。Emscripten 实现了它的性能输出 **asm.js** ，但是最近它成功地集成了一个 WebAssembly 编译目标。
*   **ASM.js** 是**Javascript 的一个底层优化子集，通过 TypedArrays 和类型注释进行线性内存访问。同样，它不是一种新的编程语言。任何没有 asm.js 支持的浏览器在运行 asm.js 时仍然可以工作，只是没有性能优势。**

**截至**10–01–2017，**目前的状态是在 [Chrome Canary](https://www.chromestatus.com/features/5453022515691520) 和 [Firefox](https://hacks.mozilla.org/2016/03/a-webassembly-milestone/) 中以一个特性标志工作，在 [Safari](https://webkit.org/status/#specification-webassembly) 中正在开发。从 V8 侧，它只是默认[被启用](https://chromium.googlesource.com/v8/v8/+/34b63f050b1a247bb64ddc91c967501ce04e011f)🚀。**

**这个来自 [Chrome Dev Summit 2016](https://www.youtube.com/playlist?list=PLNYkxOF6rcIBTs2KPy1E6tIYaWoFcG3uj) 的视频分享了 V8 中 JavaScript 和脚本工具的现状，并讨论了与 WebAssembly 的未来。**

# **构建+加载模块**

**让我们看看如何将我们的 C 程序转换成 [wasm](http://webassembly.org/docs/semantics/) 。为此，我决定使用[独立输出](https://github.com/kripken/emscripten/wiki/WebAssembly-Standalone)，而不是返回。js 和 WebAssembly 将只返回 WebAssembly 代码，而不包括系统库。**

**该方法基于 Emscripten 的[侧模块](https://github.com/kripken/emscripten/wiki/Linking)概念。侧模块在这里是有意义的，因为它是动态库的一种形式，并且不会自动链接到系统库中，它是一个自包含的编译输出。**

**`$ emcc fibonacci.c -Os -s WASM=1 -s SIDE_MODULE=1 -o fibonacci.wasm`**

**一旦我们有了二进制文件，我们只需要在浏览器中加载它。为此， [WebAssembly js api](https://github.com/WebAssembly/design/blob/master/JS.md) 公开了一个顶层对象 **WebAssembly** ，它包含了我们需要[编译](https://github.com/WebAssembly/design/blob/master/JS.md#webassemblycompile)和[实例化](https://github.com/WebAssembly/design/blob/master/JS.md#webassemblyinstance-constructor)模块的方法。下面是一个基于 [WebAssembly。最终可以用 ***WebAssembly 实例化的模块***](https://medium.com/u/649eac8108d2#webassemblymodule-constructor)。实例。**

**如果你想更深入地了解这个主题，可以看看 WebAssembly 使用的[二进制编码](https://github.com/WebAssembly/design/blob/master/BinaryEncoding.md)格式。**

# **标杆管理**

**现在是时候看看我们如何使用这个模块，并根据 javascript 实现测试它的性能了。我们将使用 4 **0** 作为输入，以与我们在上一篇文章中所做的保持一致:**

> **结果(您可以点击查看现场演示[)](https://zzarcon.github.io/WebAssembly-demo/)**

```
JS loop x 8,605,838 ops/sec ±1.17% (55 runs sampled)
JS recursive x 0.65 ops/sec ±1.09% (6 runs sampled)
JS memoization x 407,714 ops/sec ±0.95% (59 runs sampled)
Native loop x 11,166,298 ops/sec ±1.18% (54 runs sampled)
Native recursive x 2.20 ops/sec ±1.58% (10 runs sampled)
Native memoization x 30,886,062 ops/sec ±1.64% (56 runs sampled)Fastest: Native memoization
Slowest: JS recursive
```

**有趣的事实:**

*   **最好的 C 实现比最好的 JS 实现快 375%。**
*   **在 C 语言中最快的实现是内存化，而在 JS 中是循环。**
*   **第二快的 C 实现仍然比 JS 更快。**
*   **最慢的 C 实现比 JS 最慢的实现快 338 倍。**

# **结论**

**希望你们喜欢这篇关于 WebAssembly 的介绍，以及今天你们可以用它做什么。在下一篇文章中，我想介绍非独立模块，从 C JS 和 Link & Dynamic Linking 进行通信的不同技术。**

**别忘了查看 [WebAssembly 路线图](http://webassembly.org/roadmap/)和 [Emscriptend Changelog](https://github.com/kripken/emscripten/blob/master/ChangeLog.markdown) 以了解最新更新以及[入门教程](http://webassembly.org/getting-started/developers-guide/)。**

**2017 快乐🐣**

**[![](img/6b953e9462d2c008f93f889bd9b13dad.png)](https://twitter.com/zzarcon)

You can follow me on Twitter or Github @zzarcon** **[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**