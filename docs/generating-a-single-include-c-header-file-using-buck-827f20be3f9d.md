# 使用 Buck 生成单包含 C++头文件

> 原文：<https://medium.com/hackernoon/generating-a-single-include-c-header-file-using-buck-827f20be3f9d>

# TL；速度三角形定位法(dead reckoning)

使用 [Buck](https://buckbuild.com/) 为你的库生成一个单包含头文件。这为您的用户提供了一个快速开发的选项，可以在以后进行优化。看看 GitHub 上的一个[工作实例。](https://github.com/njlr/buck-single-header)

# 介绍

C++库中的一个常见模式是提供一个头文件，其中包含该库中所有其他的头文件。

例如，假设我们有一个带有这些头的库:

要使用这个库，我们需要包含我们需要的每个头文件:

或者，库设计者可以提供一个单包含头，为我们完成所有这些工作:

mathutils.hpp

用户所要做的就是包含这个标题:

## 警告

这种方法当然更方便，但也有一些缺点。

首先，单包含头可能会增加编译时间。如果我们只使用来自`add.hpp`的定义，那么包含*的所有内容*将需要解析器和预处理器做更多不必要的工作。

第二，它掩盖了你实际上所依赖的头文件。例如，在检查包含图以更好地理解项目时，这会造成混乱。

所以这是两个大的缺点，但是如果你仍然在迭代你的代码，使用单包含可能是一个好主意。为什么要在确定需要哪些标题之前优化你的 includes 呢？

# 履行

好的，作为一个库设计者，提供一个单包含头可能是一个好主意，但是我们怎样才能生成一个呢？

[巴克建立](https://buckbuild.com/)可以在这里帮助我们。如果您不熟悉它，Buck 是来自脸书的高性能构建系统。与其他解决方案相比，使用降压构建的优势在于:

*   构建是可复制的
*   构建工件可以被缓存和共享
*   巴克是跨平台的
*   它作用于哈希，而不是时间戳，这样可以防止不必要的重建
*   还有更多…

 [## 使用降压构建的 7 个理由

### Buck 是一个跨平台、跨语言构建系统，在脸书为大规模编译而设计。所有牛仔…

hackernoon.com](https://hackernoon.com/7-reasons-to-use-buck-build-5b44d7413585) 

Buck 构建脚本是用 Python 编写的，所以我们可以利用 Python 的 list 和 dictionary 函数来生成头。让我们开始吧…

*如果你只是想直奔一个工作实例，那就看看*[*GitHub repo*](https://github.com/njlr/buck-single-header)*。*

首先，我们需要获取库中所有的头文件。Buck 为此提供了一个有用的效用函数，称为`subdir_glob`。

假设这种文件布局…

还有这个剧本…

…输出将是:

一旦我们有了头文件映射，我们需要把它转换成 C++头文件。这在 Python 中相当容易:

Python’s join function is weird!

然后，我们需要创建一个`genrule`，它接受`single_header`字符串并将其写入一个文件:

最后一步是将`genrule`的输出作为头部包含在我们的`cxx_library`中。幸运的是，Buck 允许我们通过规则的名称来处理规则的输出，从而简化了这一过程:

然而，我们*也*想要包含未生成的头；我们需要合并这两本词典。Python 没有为字典提供`+`操作符，但是我们可以编写一个小函数来完成这项工作:

因此，我们的`mathutils`目标将是:

如果您构建库，那么您将在构建文件夹中看到生成的标题:

这个解决方案最好的一点是它是自动的。如果您将一个`.hpp`文件添加到`include`文件夹，那么 Buck 会自动检测到这一点，并重新生成头文件。

# 既然你在这里…

我们创建了 [Buckaroo](https://github.com/LoopPerfect/buckaroo) 来使集成 C++库变得更容易。如果你想尝试一下，最好从文档开始。您可以在 [Buckaroo.pm](https://buckaroo.pm/) 或[上浏览现有套餐，并在愿望清单](https://github.com/LoopPerfect/buckaroo-wishlist)上请求更多。

[](https://hackernoon.com/approaches-to-c-dependency-management-or-why-we-built-buckaroo-26049d4646e7) [## C++依赖管理的方法，或者我们为什么要建立 Buckaroo

### C++是一种不寻常的语言，因为它还没有一个占主导地位的包管理器(我们正在努力！).结果是…

hackernoon.com](https://hackernoon.com/approaches-to-c-dependency-management-or-why-we-built-buckaroo-26049d4646e7)