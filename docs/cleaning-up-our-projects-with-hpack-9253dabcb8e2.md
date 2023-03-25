# 用 Hpack 清理我们的项目

> 原文：<https://medium.com/hackernoon/cleaning-up-our-projects-with-hpack-9253dabcb8e2>

大约一个月前，我们发布了免费的[堆栈迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)。如果你以前从未使用过 Stack，你完全应该去看看那个课程！这篇文章将让你先睹为快，了解课程中的一些内容。

但是，如果您已经熟悉堆栈的基础知识，并且认为您不需要该课程，请不要担心！在本文中，我们将通过另一个很酷的工具来简化您的工作流程！

几乎所有你创建的 Haskell 项目都会用到 Cabal。Cabal 为你执行了几项重要的任务。它为您下载依赖项，并在文件系统中定位您编写的代码。它还链接你所有的代码，这样 GHC 就可以编译它。为了让 Cabal 做到这一点，你需要创建一个描述所有这些事情的`.cabal`文件。例如，它需要知道你的代码的每一部分依赖于什么库。您还必须指定源文件目录在文件系统中的位置。

一个`.cabal`文件的组织有时有点混乱。语法可能相当冗长。如果我们使用“Hpack”工具，我们可以使我们的生活更简单。Hpack 允许您以更简洁的格式指定您的项目组织。一旦指定了 Hpack 格式的所有内容，就可以用一个命令生成`.cabal`文件。

# 使用 Hpack

使用 hpack 的第一步当然是下载它。这很简单，只要你在你的系统上安装了 Stack。使用命令:

```
stack install hpack
```

这将在系统范围内安装 Hpack，这样您就可以在所有项目中使用它。下一步是在一个名为`package.yaml`的文件中指定代码的组织。这里有一个简单的例子:

```
name: HpackExampleProjectversion: 0.1.0.0ghc-options: -Walldependencies:
  - baselibrary:
  source-dirs: src/executables:
  HpackExampleProject-exe:
    main: Main.hs
    source-dirs: app/
    dependencies:
      HpackExampleProjecttests:
  HpackExampleProject-test:
    main: Spec.hs
    source-dirs: test/ 
    dependencies:
      HpackExampleProject
```

这个例子将生成一个非常简单的 cabal 文件。它看起来很像运行`stack new HpackExampleProject`的默认模板。有几个基本的字段，比如我们项目的名称、版本和编译器选项。我们可以指定关于我们的代码库、可执行文件和任何测试套件的细节，就像我们可以在一个`.cabal`文件中一样。这些组件中的每一个都可以有自己的依赖关系。我们还可以指定全局依赖关系。

一旦我们创建了这个文件，我们需要做的就是从包含这个文件的目录中运行`hpack`命令。这将生成我们的`.cabal`文件:

```
>> hpack
generated HpackExampleProject.cabal
```

# Hpack 解决了哪些问题？

hpack 解决的一个大问题是模块推理。您的`.cabal`文件应该指定作为您的库的一部分的所有 Haskell 模块。您总是有两个组:“公开的”模块和“其他”模块。列出这些模块中的每一个都是很烦人的，随着你的库变得越来越大，这个列表会变得很长！更糟糕的是，当您创建了一个新模块，但忘记将它添加到。阴谋文件。使用 Hpack，您不需要记住添加大多数新文件！它会查看您的文件系统，并确定为您提供了哪些模块。假设您已经在源目录中这样组织了文件:

```
src/Lib.hs
src/API.hs
src/Internal/Helper.hs
src/Internal/Types.hs
```

使用正常的。`cabal`方法，您需要手动列出这些模块。但是没有在 package.yaml 文件中列出任何内容，您将在。cabal 文件:

```
exposed-modules:
  API
  Internal.Helper
  Internal.Types
  Lib
```

现在，您可能不希望所有的模块都公开。您可以对程序包文件进行简单的修改:

```
library:
  source-dirs: src/
  exposed-modules:
    - API
    - Lib
```

并且 hpack 将纠正`.cabal`文件。

```
exposed-modules:
  API
  Lib
other-modules:
  Internal.Helper
  Internal.Types
```

从这一点来看，Hpack 会将所有新的 Haskell 模块推断为“其他”模块。您只需要在 package.yaml 中列出“暴露的”模块。只需要记住一件事！你需要在每次添加新模块时运行`hpack`，否则 Cabal 不知道你的代码在哪里。这仍然比每次修改`.cabal`文件容易得多。`.cabal`文件本身仍然包含一长串模块名。但是它们中的大部分不会出现在`package.yaml`文件中，这是你主要的交互点。

使用 hpack 的另一个好处是全局依赖性。请注意，在示例中，我们在所有其他部分的上方有一个“dependencies”字段。这意味着我们的库、可执行文件和测试套件都将免费获得 base 作为依赖项。如果没有 hpack，我们将需要在每个单独的部分中指定 base 作为依赖项。

hpack 还提供了大量其他语法。一个简单的例子是 github 规范。您可以将以下单行放入您的程序包文件中:

```
github: username/reponame
```

您将在您的`.cabal`文件中免费获得以下代码行。

```
homepage:          https://github.com/username/reponame#readme
bug-reports:       [https://github.com/username/reponame/issues](https://github.com/username/reponame/issues)
```

# 摘要

一旦你超越了玩具项目，你的包的维护将不是微不足道的。如果你使用 hpack，你将更容易组织你的第一个大项目。语法更清晰。组织更直观。最后，你将为自己省去执行许多重复任务的压力。对`.cabal`文件的不断编辑将会中断您的流程和构建过程。所以避免它们会让你更有效率。

如果你以前从未使用过 Stack 或 Cabal，这里有很多东西需要掌握。但是希望你相信有简单的方法来组织你的 Haskell 代码！如果您对如何学习感兴趣，请注册参加我们免费的 [Stack 迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)！您将了解到组织 Haskell 项目的所有简单方法。

如果您从来没有使用过 Haskell，并且对这一切感到完全困惑，没有必要烦恼！下载我们的[入门清单](https://www.mmhaskell.com/checklist)，你将踏上学习 Haskell 的征途！