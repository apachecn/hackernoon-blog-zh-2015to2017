# 又一个 sbt-docker 介绍

> 原文：<https://medium.com/hackernoon/yet-another-sbt-docker-introduction-2d9fb99fe367>

最近，该团队一直在寻求以自动方式创建在我们的 Docker 环境中使用的 Docker 图像。

这些图像目前是通过定义 Dockerfile 手动创建的，但这个过程与我们的构建过程完全分离，每次图像本身的内容发生变化时，它都会使我们陷入非常糟糕的境地。我们每次都需要手动更新这些文件。

为了克服这种*小*的不便，我们开始寻找替代品。当然，sbt-native-packager 看起来是这项工作的合适工具，因为我们将它用于其他打包格式，所以我们开始对它进行测试。

有很多关于如何使用 sbt-native-packager 创建 Docker 映像的文档，我不打算赘述。然而，在更复杂的构建中使用插件几乎没有什么发现，特别是如果我们在模块之间有依赖关系或者想要为我们项目的选定模块创建映像。

为了展示我们的需求，让我们来看一个我们可以使用的项目布局的例子。

```
├── README.md
├── build.sbt
├── core
├── json-processor
├── json-sub
├── project
├── src
├── target
└── version.sbt
```

在这里，我们有一个多项目构建，包含 3 个项目或子模块，*， ***json 处理器，json-sub*** 。内核内部是我们的主要抽象，由其他两个模块共享。核心模块没有自己运行的能力。这是另外两个项目的责任。*

*让我们看看位于根文件夹中的 ***build.sbt*** 文件，以便更好地了解我们的构建是如何工作的。*

```
*name := "sample-app"
version := "1.0"
scalaVersion := "2.11.7"

lazy val root = project.in(file("."))
  .aggregate(core, jsonProcessor, jsonSub)
  .dependsOn(core, jsonProcessor, jsonSub)

  lazy val core = project.in(file("core"))
  .settings(
    name := "core"
  )

  lazy val jsonProcessor = project.in(file("json-processor"))
  .aggregate(core)
  .dependsOn(core)

lazy val jsonSub = project.in(file("json-sub"))
  .aggregate(core)
  .dependsOn(core)*
```

*正如我们所看到的，没有什么新的东西，我们之前提到的相同模块正在被定义，包括对模块核心的依赖。*

## *Sbt-Docker*

*以同样的方式，其他教程添加这个插件，我们只是把它和 sbt-native-packager 一起添加到我们的 plugins.sbt 文件。*

```
*addSbtPlugin("com.typesafe.sbt" % "sbt-native-packager" % "1.1.1")
addSbtPlugin("se.marcuslonnberg" % "sbt-docker" % "1.4.0")*
```

*现在，我们需要激活我们想要用来创建 Docker 图像的模块中的插件。正如您所想象的，在根项目上这样做没有什么意思，因为它只是项目组织的一个人工集合，别无其他。核心模块也是如此，它不能自己运行，所以我们不需要插件。另一方面，另外两个模块是我们感兴趣的。*

**需要注意的是，在我们找到的每一篇教程/帖子/文章中，下面的键和定义都是在根项目中定义的，但是我们将把注意力集中在实际执行项目的两个模块上，以避免这种情况。**

*为了增加构建 docker 文件和图像的能力，我们需要在相关模块上激活 sbt-docker 插件。这是一个非常简单的任务。*

```
*lazy val jsonProcessor = project.in(file("json-processor"))
  .aggregate(core)
  .dependsOn(core)
  .enablePlugins(sbtdocker.DockerPlugin, JavaServerAppPackaging)

lazy val jsonSub = project.in(file("json-sub"))
  .aggregate(core)
  .dependsOn(core)
  .enablePlugins(sbtdocker.DockerPlugin, JavaServerAppPackaging)*
```

*注意每个模块上的***enable plugins***部分。*

*此时，我们应该能够为这两个模块创建 Docker 映像，该任务通过运行以下命令来实现:*

```
*sbt "jsonProcessor/docker"*
```

*或者*

```
*sbt "jsonSub/docker"*
```

*这些命令中的任何一个都应该创建一个 Docker 文件，然后将映像发布到本地 Docker 注册表。然而，如果我们尝试运行任何最近创建的映像，我们将很快注意到模块核心上的类将找不到。*

*解决方案很简单，但是我们花了很少的时间就想出来了。*

*我们需要确保核心模块包含在由 ***jsonProcessor*** 和 ***jsonSub*** 创建的映像上。我们需要在构建过程中定义一个依赖关系，表明我们需要核心模块包含在我们自己的(每个模块)映像中。*

*一个有趣的不太复杂的方法是使用 ***sbt-assembly*** 插件。它将负责 bundle core 及其依赖项，因此我们可以在创建 Docker 映像之前将输出 jar 文件(fat jar)添加到我们自己的(模块)打包过程中。*

*在这一点上，我们还需要指定一个定制的 Docker 映像，以便它符合我们包含核心的要求。*

```
*lazy val dockerSettings = Seq(
  docker <<= (docker dependsOn (AssemblyKeys.assembly in core)),

  dockerfile in docker := {
    val artifact: File = (AssemblyKeys.assemblyOutputPath in AssemblyKeys.assembly in core).value
    val artifactTargetPath = s"/app/${artifact.name}"
    new Dockerfile {
      from("java")
      add(artifact, artifactTargetPath)
      entryPoint("java", "-jar", artifactTargetPath)
    }
  }
)*
```

*让我们解释一下 dockerSettings 是如何定义的:*

*首先，我们使 docker 任务依赖于核心模块的组装任务。这意味着在继续执行 docker 任务之前，需要将 core 组装到一个单独的 jar 文件中。*

*其次，我们已经定义了 docker 文件的外观。基于这个文件，docker 将继续创建相应的 Docker 映像。*

*变量工件定义了由 core 上的 ***assembly*** 任务创建的 fat jar，我们继续使用 Docker add 命令将这个 fat jar (core)添加到我们正在构建的映像中。*

*该任务的结果将是一个 Docker 映像，其中包含核心的 fat jar 以及任何其他库依赖项。*

*现在我们需要指出我们想要图像的两个模块将使用设置(dockerSettings)。*

```
*lazy val jsonProcessor = project.in(file("json-processor"))
  .aggregate(core)
  .dependsOn(core)
  .enablePlugins(sbtdocker.DockerPlugin, JavaServerAppPackaging)
  .settings(dockerSettings)

lazy val jsonSub = project.in(file("json-sub"))
  .aggregate(core)
  .dependsOn(core)
  .enablePlugins(sbtdocker.DockerPlugin, JavaServerAppPackaging)
  .settings(dockerSettings)*
```

*注意，我们已经为每个模块添加了***docker settings***。*

*在这之后，我们应该可以运行:*

```
*sbt "jsonProcessor/docker"*
```

*或者*

```
*sbt "jsonSub/docker"*
```

*并且创建的图像应该可以使用了。*

*此时，我们正在使用 sbt-docker 插件，但是请记住，根项目并没有激活插件，这意味着在运行 docker 任务时，我们总是需要指定项目名称。为了避免这种情况，我们还添加了一个 sbt 命令别名来简化这个过程。*

```
*addCommandAlias("dockerize", ";jsonProcessor/docker;jsonSub/docker")*
```

*添加这个别名后，我们应该能够执行:*

```
*sbt dockerize*
```

*在我们的根项目和 sbt 将确切地知道该做什么。*

*您可能还想使用其他有趣的 sbt-docker 选项，但它们完全是可选的。我们希望为每个模块创建两个图像。一个图像的名称是模块的名称，并标记模块的版本，另一个图像具有相同的名称，但标记为 ***latest。*** 如果我们计划使用 *docker-compose* 这是非常有用的，因为我们不需要在每次项目版本改变时改变 *compose* 文件上的标签。我们可以简单地一直使用*，并将带有版本号的图像作为我们曾经创建的所有图像的历史。**

**为此，我们将***docker settings***更改如下:**

```
**lazy val dockerSettings = Seq(
  docker <<= (docker dependsOn (AssemblyKeys.assembly in core)),

  dockerfile in docker := {
    val artifact: File = (AssemblyKeys.assemblyOutputPath in AssemblyKeys.assembly in core).value

    val artifactTargetPath = s"/app/${artifact.name}"

    new Dockerfile {
      from("java")
      add(artifact, artifactTargetPath)
      entryPoint("java", "-jar", artifactTargetPath)
    }
  },

  imageNames in docker := Seq(
    // Sets the latest tag
    ImageName(s"${organization.value}/${name.value}:latest"),

    // Sets a name with a tag that contains the project version
    ImageName(
      repository = name.value,
      tag = Some("v" + version.value)
    )
  )
)**
```

**基本上，我们只是添加了 ***imageNames*** 部分的设置。同样，这将为每个项目创建两个图像，正如我们上面解释的那样。**

**最后，如果我们的项目出了问题，我们不想构建这些图像。我们希望确保这些图像具有经过验证的工作代码。为此，我们扩展了 sbt 命令别名。**

```
**addCommandAlias("dockerize", ";clean;compile;test;jsonProcessor/docker;jsonSub/docker")**
```

**现在，它将在创建图像之前进行所有必要的验证。**

**通过自动化这一过程，我们消除了产品化的繁琐的手动步骤。使用一个简单的命令，我们就有了完整的管道，所以我们的代码可以很容易地部署到我们的 Docker 环境中。我们还展示了如何创建 sbt 任务依赖关系，以及如何访问我们需要的每个必要部分。**

**请记住，我们正在使用 sbt-docker 插件以及 sbt-native-packager 和 sbt-assembly。sbt-native-packager 仍然缺少 sbt-docker 插件的一些功能，并且没有最新版本灵活。**

**我们强烈建议看一看关于这个主题的其他教程，因为它们提供了关于您可能需要的其他部件和过程的扩展信息。然而，构建依赖于项目子模块的映像是我们在其他地方没有发现的。**

**我们希望这个小指南能帮助你走向码头工人的世界。**

> **黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**