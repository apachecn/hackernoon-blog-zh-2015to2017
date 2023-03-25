# 如果你没有用 Rust 优化你的网络应用，你就错过了

> 原文：<https://medium.com/hackernoon/youre-missing-out-if-you-don-t-optimize-your-web-app-with-rust-416308ab3fb2>

![](img/b8a125e1f5fd93b9b2a216f6b365c87e.png)

Mozilla 的软件工程师 Brian Anderson 在 Mozilla 的宣传视频中说:“Rust 是一种秘密武器。这并没有真正解释 Rust 是什么，以及为什么您想在 web 应用程序中使用它。我们将通过回答一些常见问题来揭开 Rust 的神秘面纱。

# **铁锈像红宝石还是蟒蛇？**

不完全是。

从表面上看，Rust 是一种**通用语言**，最初是 Graydon Haore 的一个激情项目。它现在是一个开源项目，由 Mozilla Research 发布。从理论上讲，你可以在 Rust 中构建任何东西，从华而不实的 web 应用程序到高效的 web 服务器。不过，它主要被宣传为一种系统编程语言，这意味着 Rust 可能不会很快与 Ruby/Rails 竞争。

Rust 与 C/C++ 更紧密地联系在一起，因为它是一种静态类型的低级语言。作为 web 开发人员，我们所习惯的是 Javascript 提供给我们的动态类型，其中字符串不必定义为 string，整数不必定义为 int。像 C/C++一样，Rust 可以充分利用机器的处理能力来创建高性能的系统。

# **Rust c++也是如此？**

不完全是。

Rust 承诺的是没有所有安全问题的 C++控制，比如 segfaults、空指针、缓冲区溢出和许多其他 C++开发人员不得不面对的安全噩梦。它的独特卖点是检查系统，可以在编译时喊出错误信息，这是老化的 C++语言所不具备的。从 web 开发人员的角度来看，错误似乎是一个微小的改进，但是对于低级程序员来说，被警告内存分配不当可能意味着代码损坏和生产就绪代码之间的区别。

Rust 还抽象出了程序员执行指针运算和内存管理的需求，而不会以性能速度为代价。这是通过 YouTube 视频[中的所有权概念来实现的。](https://www.youtube.com/watch?v=agzf6ftEsLU)

# **我为什么要使用 Rust？**

**Rust 是一种模块化语言**。假设您想优化用 JavaScript 编写的 web 应用程序的一个方面。您可以使用像 asm.js 这样的中间语言来编译 Rust 代码和 JavaScript 代码，而不是一头扎进 C++。因为 Rust 代码是安全的，所以你不必担心在你的程序中引入应用崩溃错误。

作为现代开发人员，我们已经习惯了流行的软件包管理工具如 npm、rubygems 和 pypi 提供给我们的开源生态系统。Rust 附带了一个名为 Cargo 的工具，它允许您像输入 npm install 命令一样导入库。

# **遗言**

Rust 承诺了并发运行的安全代码，就像许多其他高级语言一样。同时，其惊人的速度借鉴了类 C 语言。总的来说，这种语言的混合性质使它成为一种通用的工具，如果本地优化技巧和附加组件不奏效的话，可以方便地使用。

**边注:** [Geregely Nemeth](https://blog.risingstack.com/how-to-use-rust-with-node-when-performance-matters/) 结合 Rust 和 Cargo，向节点开发者展示如何将 Rust 编程语言与 Node 集成。如果你仍然持怀疑态度，读一读他的[帖子](https://blog.risingstack.com/how-to-use-rust-with-node-when-performance-matters/)，你会发现把 Rust 嵌入你的应用程序是多么简单。

拉吉·阿因拉| rajiayinla858@gmail.com