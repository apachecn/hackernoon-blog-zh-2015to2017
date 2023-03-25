# 我们将 C++库作为源代码发布的 6 个原因

> 原文：<https://medium.com/hackernoon/6-reasons-why-we-distribute-c-libraries-as-source-code-2dc614d5ef1e>

![](img/7362e75089a9d1c4947a9b428bb6ffb7.png)

It’s JavaScript… which is distributed as source-code! (Photo by [Markus Spiske](http://unsplash.com/photos/xekxE_VR0Ec?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText))

编写 C++应用程序时，不可避免地会用到外部库。这是好事！代码重用使我们更有生产力，让我们构建更大更好的产品。有了 [Buckaroo.pm](http://buckaroo.pm) ，我们试图用一个 C++的纯源代码包管理器让这变得更简单。

仅限源代码？是的，原因如下:

# 1.它是跨平台的

假设 C++代码适当地抽象掉了任何使用的系统库，C++源代码是完全可移植的。然而，一旦被编译，这个属性就丢失了，因为二进制文件必须以特定的指令集为目标。

# 2.调试体验更好

在调试项目时，能够深入研究您所使用的库函数的实现是一件好事。当您从源代码构建时，这变得特别容易，因为您所依赖的确切代码已经在您的机器上了！

# 3.没有 ABI 兼容性问题

GCC 的用户会注意到`std::string` ABI 从 4.9 到 5.0 的变化。虽然这个改变对于转换到 C++ 11 是必要的，但是它[在社区中引起了很多混乱](https://stackoverflow.com/questions/34571583/understanding-gcc-5s-glibcxx-use-cxx11-abi-or-the-new-abi)。如果您已经从源代码构建了项目，那么升级是一件容易的事情。😌

# 4.源代码更小

由于其代码生成特性，C++尤其如此:库的源代码通常远小于其对应的二进制代码。事实上，对于可复制的构建，源代码是一种极好的压缩形式！

# 5.可以正确支持编译器标志

带有 1 个编译器开关的库有 2 个可能的编译二进制文件。带有 2 个编译器开关的库有 4 个可能的二进制文件。具有 3 个编译器开关的库有 8 个可能的二进制文件。具有 4 个编译器开关的库有 16 个可能的二进制文件。现在混合使用交叉编译...你明白了。并非所有这些组合都很重要，但复杂性是巨大的。当从源代码分发时，我们可以通过允许用户控制进程来支持所有可能的编译器标志设置。

# 6.更好的优化

C++就是建立抽象的塔，编译器可以把它分解成高效的机器代码。这些抽象在编译后会丢失，可能用于优化代码的重要信息也会随之丢失。使用二进制库时，您会失去执行许多跨库优化的机会。从源代码构建可以产生更快的代码。

# 但是编译时间呢？

这就是好的构建系统至关重要的地方。我们选择了 [Buck build](https://buckbuild.com) ，一个由脸书开发和使用的可复制的构建系统。因为 Buck 是可复制的，所以缓存中间构建工件并在您的团队中共享它们成为可能！

阅读[使用降压构建的 7 个理由](https://hackernoon.com/7-reasons-to-use-buck-build-5b44d7413585)。

# 准备好试试牛仔了吗？

[Buckaroo](http://buckaroo.pm) 是一个源代码专用的 C++包管理器。首先，请阅读文档。您可以在 [Buckaroo.pm](http://buckaroo.pm) 上浏览现有套餐，或者在[愿望清单](https://github.com/LoopPerfect/buckaroo-wishlist)上请求更多套餐。