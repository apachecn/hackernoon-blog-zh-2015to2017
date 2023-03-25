# 如何创建一个基于 Buck 的 C/C++项目

> 原文：<https://medium.com/hackernoon/how-to-create-a-buck-based-c-c-project-38b85273d6a6>

![](img/f68ffd96179039a719531838fabb705b.png)

Buck 是由脸书开发的快速构建工具。选择 Buck 的理由有[很多，但是应该如何入门呢？](https://hackernoon.com/7-reasons-to-use-buck-build-5b44d7413585)

该演练将涵盖:

*   如何组织你的项目
*   Google 测试的集成
*   基本降压命令的解释

## TL；速度三角形定位法(dead reckoning)

在 GitHub 上浏览[项目文件，或者将它们克隆到您的系统上:](https://github.com/njlr/buck-cpp-example)

```
git clone [git@github.com](mailto:git@github.com):njlr/buck-cpp-example.git
```

从 [Buckaroo.pm](https://www.buckaroo.pm) 安装现有的降压口。

# 巴克是如何工作的

在我们开始之前，了解一下 Buck 的工作原理可能会有所帮助。与其旧事重提，不如看看巴克开发者的这篇演讲。

Ludicrously Fast Builds with Buck

# 安装降压器

安装 Buck 的官方指南[可在他们的网站](https://buckbuild.com/setup/getting_started.html)上获得。然而，最快的方法可能是使用 Homebrew 或 Linuxbrew。

*   如果你使用的是 Linux，你可以在他们的网站上使用[一行程序安装 Linuxbrew。](http://linuxbrew.sh/)
*   macOS 上的[自制](https://brew.sh/)同上。

一旦你有一个“酿造”安装，添加脸书水龙头，并安装降压:

```
brew tap facebook/fb
brew install --HEAD facebook/fb/buck
```

使用以下内容验证您的安装:

```
buck --version
```

# 整理您的文件

一旦安装了 Buck，就该创建项目文件夹了。Buck 使得支持你喜欢的任何文件夹结构变得容易，但是对于这个演示，我们将遵循 C/C++的惯例，使用`src`和`include`文件夹。

该项目将由两部分组成:

*   `demo` —计算`3 + 4`并打印结果的可执行文件
*   `mathutils` —一个提供简单添加功能的库

*注意:这是一个用于演示目的的简单示例；buck capable to build*[*复杂的 C/C++项目*](http://buckaroo.pm/search?q=*) *！*

首先，在您的驱动器上创建以下结构:

```
.
├── .buckconfig
├── BUCK
└── demo
    ├── include
    └── src
        └── main.cpp
```

使用命令行:

```
touch .buckconfig
touch BUCKmkdir demo
mkdir demo/include
mkdir demo/src
touch demo/src/main.cpp
```

这是相当多的文件，让我们浏览一下:

*   在运行任何降压命令之前，需要一个`.buckconfig`。它告诉 Buck 项目的根在哪里，也可以用来配置项目的全局设置。现在我们可以让它空着。
*   `BUCK`文件是我们为二进制文件定义目标的地方。在一个 Buck 项目中可以有多个`BUCK`文件，这在您想要为项目的不同方面(比如库和测试)分离构建逻辑时非常有用。
*   `demo/include`是我们放置二进制文件使用的头的地方。
*   `demo/src`是我们放置二进制文件翻译单元(这里是`.cpp`文件)的地方。
*   `demo/src/main.cpp`将是我们应用的切入点。

## 主页面

首先，我们将编写一个简单的 hello-world 程序。将以下内容粘贴到`main.cpp`:

demo/src/main.cpp

## 钱

为了构建`main.cpp`文件，我们需要为它编写一个 Buck 目标。将以下内容粘贴到`BUCK`文件中:

BUCK

文件是用 Python 2 的一种方言写的，有一些扩展名。当您调用`buck build`时，Buck 将执行 Python 并记录定义的任何目标。一旦目标列表被解析，每个目标就根据其类型被构建。

您可以在[降压文档](https://buckbuild.com/rule/cxx_binary.html)中看到完整的目标类型列表，但是对于 C/C++来说重要的是`cxx_binary`、`cxx_library`和`prebuilt_cxx_library`。

*   `cxx_binary` —包含入口点的一组 C/C++翻译单元和头(例如`int main()`)。一个`cxx_binary`一旦编译就可以执行。不应该是依赖。
*   `cxx_library` —可以被其他目标使用的一组 C/C++翻译单元。与`cxx_binary`不同，一个库还定义了一个`exported_headers`列表，它是对其依赖者可用的头文件。
*   `prebuilt_cxx_library` —像一个`cxx_library`，但是在翻译单元的位置有一个可选的目标文件。只有头文件的库被实现为没有目标文件的`prebuilt_cxx_library`。

# 降压命令

现在`BUCK`文件已经就绪，Buck 可以构建目标了。运行以下命令:

```
buck build //:demo
```

该命令告诉 Buck 构建在与`.buckconfig`相邻的`BUCK`文件中找到的目标`:demo`。

Buck 基于项目的实际文件夹结构，对目标使用简单的寻址系统。例如，`//examples/basic/:demo`是指`examples/basic/BUCK`中定义的目标`demo`。

构建完成后，您应该在`buck-out/gen/demo`找到一个可执行文件。您可以使用以下代码构建并运行它:

```
buck build //:demo && ./buck-out/gen/demo
```

或者，巴克可以帮你做:

```
buck run //:buck-cpp-example
```

您会注意到，第二次运行构建的速度非常快。这是因为 Buck 缓存了所有东西，包括 Python 脚本的输出！

# 添加依赖关系

让我们实现`mathutils`以便在演示应用程序中使用它。

在项目中创建以下文件夹结构:

```
.
└── mathutils
    ├── BUCK
    ├── include
    │   └── add.hpp
    └── src
        └── add.cpp
```

使用命令行:

```
mkdir mathutils
mkdir mathutils/include
mkdir mathutils/srctouch mathutils/BUCK
touch mathutils/include/add.hpp
touch mathutils/src/add.cpp
```

和文件本身:

mathutils/add.hpp

mathutils/add.cpp

mathutils/BUCK

关于这个`BUCK`文件有几个要点:

*   将`header_namespace`设置为`'mathutils'`。这会将该库导出的每个头文件放入一个同名文件夹中，从而减少与其他库发生文件名冲突的可能性。
*   `glob`规则的根在`BUCK`文件，所以`src/**/*.cpp`实际上是从项目根对应`mathutils/src/**/*.cpp`。
*   可见性被设置为`//...`,以便该目标可以被项目中的所有其他目标作为依赖项。在英语中，它的意思是“这个库对根目录下的所有其他目标都是可见的”。

## 使用添加功能

现在我们可以在`demo`可执行文件中使用`mathutils`库。

首先，声明`demo`对`mathutils`的依赖。将项目根目录下的`BUCK`文件改为:

BUCK

现在更新`main.cpp`到:

demo/src/main.cpp

使用 Buck 运行`demo`查看结果。你会注意到巴克知道如何为你连接`mathutils`。

# 谷歌测试

我们的应用程序正在工作，但是为了勤奋，我们应该添加一些单元测试！

Buck 通过`buck run`支持所有的 C/C++测试框架，但是它提供了与 Google Test 的额外集成。

## 获取 Google 测试源代码

Git 提供了一种使用子模块获取 Google 测试源代码的简单方法。我们将使用包含一个`BUCK`文件的[fork](https://github.com/njlr/googletest)，但是如果需要的话，您可以使用主文件并编写自己的文件。

```
git submodule add git@github.com:njlr/googletest.git
cd googletest/
git checkout 48072820e47a607d000b101c05d796ebf9c4aad2
cd ../
```

现在我们需要告诉 Buck 在哪里可以找到 Google 测试源。打开`.buckconfig`并添加以下内容:

.buckconfig

这告诉 Buck 在哪里可以找到可以用于测试的 Google 测试目标。还可以设置其他配置属性；浏览 [Buck 文档](https://buckbuild.com/concept/buckconfig.html)。

## 编写测试

我们将把测试放在`mathutils/test`中，旁边是`mathutils/src`和`mathutils/include`:

```
.
└── mathutils
    ├── BUCK
    ├── include
    │   └── add.hpp
    ├── src
    │   └── add.cpp
    └── test
        ├── BUCK
        └── add.cpp
```

使用命令行:

```
mkdir mathutils/test
touch mathutils/test/add.cpp
```

测试本身:

mathutils/test/add.cpp

最后，我们需要在`BUCK`文件中声明测试:

mathutils/test/BUCK

现在测试可以由巴克进行了:

```
buck test //mathutils/test:add
```

或者，运行所有测试:

```
buck test
```

# 结论

就是这样！Buck 是一个强大的工具，它将为你节省项目开发周期中的等待时间。要了解更多信息，请阅读[文档](https://buckbuild.com/setup/getting_started.html)或观看一些 [Buck 演示](https://buckbuild.com/presentations/index.html)。

如果有你需要移植到 Buck 的库，看看 [Buckaroo.pm](https://www.buckaroo.pm/) 。我们已经移植了 300 个项目，并且正在进行更多的工作！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> 黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)