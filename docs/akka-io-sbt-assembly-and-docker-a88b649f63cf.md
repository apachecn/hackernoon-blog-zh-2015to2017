# Akka.io，SBT-装配和对接

> 原文：<https://medium.com/hackernoon/akka-io-sbt-assembly-and-docker-a88b649f63cf>

我们使用 akka 功能已经有一段时间了，但我们也开始探索我们现在拥有的 actor 系统的 Docker 部署。在我之前的两篇帖子中， [***还有另一篇 sbt-docker 简介***](https://hackernoon.com/yet-another-sbt-docker-introduction-2d9fb99fe367#.52uwpg3bt) ，以及*[***Akka 集群在 docker 中，简单明了地配置了***](https://hackernoon.com/akka-cluster-in-docker-a-straight-forwards-configuration-b6deea32752d#.x5cpvjl0s)*我们已经探索过如何使用一些工具，直接嵌入到我们的构建管道中，来做部署到 Docker 中。然而，我们的团队提出了一个问题，*我们是应该创建一个胖罐子并将其放在 docker 上，还是应该将所有依赖项一起进行部署？***

# **sbt 组件**

**直接从文档中**

> **sbt-assembly 是一个 sbt 插件，最初移植自 codahale 的 assembly-sbt，我猜是受到了 Maven 的 assembly 插件的启发。目标很简单:为您的项目及其所有依赖项创建一个大容器。**

**这是一个很好的工具，对于管理依赖性非常有用。使用 sbt-assembly，我们可以创建一个单独的 JAR，然后将它添加到 Docker 中。让我们把 sbt-docker 和 sbt-assembly 插件结合起来。**

```
**lazy val dockerSettings = Seq(
    dockerfile in docker := {
        val artifact: File = assembly.value
        val artifactTargetPath = s"/app/${artifact.name}"

        new Dockerfile {
          from("java")
          add(artifact, artifactTargetPath)
          entryPoint("java", "-jar", artifactTargetPath)
        }
  },

  ....
}**
```

**这个 sbt 设置将创建 fat JAR，将其添加到 Docker 映像中，并创建一个入口点，这样当容器启动时，我们的应用程序就会运行。**

**这里没有什么特别的，这应该适用于任何应用程序，除非它不工作。**

**问题不在于插件，而在于 Akka 本身。Akka 文档说每个模块依赖于它自己的配置，也就是说我们需要多个 Akka 配置文件。这就给我们带来了下一个问题，如果 Akka 用默认值生成配置文件，它们将如何在 sbt-assembly 过程中被合并？如果你跟随 [*这个链接*](http://letitcrash.com/post/21706121997/follow-up-sbt-assembly-now-likes-referenceconf) ，你会意识到这已经为我们解决了。尽管如此，我们还是想提供一个名为 *application.prod.conf* 的配置文件，它位于 *resources/confs/* 目录中，让我们看看如何实现。**

```
**lazy val dockerSettings = Seq(
    dockerfile in docker := {

         val dockerFiles = {
            val resources = (unmanagedResources in Runtime).value
            val dockerFilesDir = resources
                .find(_.getPath.endsWith("/confs"))
                .get
            resources
                .filter(_.getPath.contains("/confs/"))
                .map(r => 
                    dockerFilesDir
                        .toURI
                        .relativize(r.toURI)
                        .getPath -> r)
                .toMap
          }

        val artifact: File = assembly.value
        val artifactTargetPath = s"/app/${artifact.name}"

        new Dockerfile {
          from("java")
          add(artifact, artifactTargetPath)
          add(dockerFiles(
            "application.prod.conf"),
            ("/app/application.prod.conf")
          )
          entryPoint(
            "java", 
            "-Dconfig.resource=/app/application.prod.conf", 
            "-jar", 
            artifactTargetPath
          )
        }
  },

  ....
}**
```

**在这里，我们已经添加了我们自己的配置，并在入口点指定我们希望将它用作资源。现在，我们的 Docker 映像将拥有 fat JAR 和我们将用作资源的配置。**

**这种方法工作得很好，但是我们不喜欢这个过程。与这些容器相关的构建时间很长，因为 sbt 必须清理所有东西，然后编译、运行测试、组装所有东西(真的需要很长时间)并创建 Docker 映像。你可能会认为这是一个小问题，但是如果你每次都要构建一百个图像，相信我，这需要相当长的时间。**

## **组装还是不组装**

**从您的应用程序创建一个单独的 JAR 在很多场景下都是有意义的，但是如果我们要在 Docker 上部署，真的值得经历所有这些耗时的过程吗？**

**嗯，我们的应用程序有一个独特的 JAR 的想法是封装依赖关系，这使我们能够更有效地管理我们的发布过程。只有一个里面装着所有东西的罐子，最终还是很方便的。但是，Docker 背后不也是这样的想法吗？Docker 映像是我们的应用程序的封装，可以部署在任何地方，在这种情况下，它取代了(或像以前一样)使用单个 JAR 的想法。**

**这些是我们作为一个团队讨论过的主张没有脂肪罐的集装箱化的想法。**

**让我们回顾一下所需的更改。**

**首先，我们从 ***plugins.sbt*** 中移除 *sbt-assembly* ，取而代之的是 *sbt-native-packager* 。**

**第二，我们改变了在我们的 ***build.sbt*** 中创建 Docker 映像的方式**

```
**lazy val dockerSettings = Seq(
    dockerfile in docker := {
         val dockerFiles = {
            val resources = (unmanagedResources in Runtime).value
            val dockerFilesDir = resources
                .find(_.getPath.endsWith("/confs"))
                .get
            resources
                .filter(_.getPath.contains("/confs/"))
                .map(r => 
                    dockerFilesDir
                        .toURI
                        .relativize(r.toURI)
                        .getPath -> r)
                .toMap
          } val appDir: File = stage.value
        val targetDir = "/app"

        new Dockerfile {
          from("java")
          add(appDir, targetDir)
          add(dockerFiles(
            "application.prod.conf"),
            ("/app/application.conf"))          
          entryPoint(
              s"$targetDir/bin/${executableScriptName.value}"
          )
        }
    },

 ...

}**
```

**注意，现在我们不再依赖于汇编，而是使用 *stage.value* 。换句话说，我们将由*阶段*生成的内容添加到我们容器中的 **/app** 中。还有，切入点变了。 *sbt-native-packager* 使用一个启动脚本代替 *java -jar* 命令。记住，我们需要将 *application.prod.conf* 文件添加到其中，但是我们不想使用额外的 JVM 参数，我们将其重命名为默认的 *application.conf* 。**

**最终，不创建 fat JARs 的改变减少了我们 80%的构建时间，这是我们最大的问题。**

# **最後的**

**有时我们不知道该做什么，特别是在软件工程中，但是直到我们测试了不同的方法，看看哪一个最适合我们的需要。**

**从来没有唯一的解决方案，大部分时间都是在各种想法的混合中找到我们的解决方案。在我们的例子中(Docker 上的 akka)，sbt-assembly 很好，但是增加了太多的构建时间。另一方面，Docker 是一个必须拥有的平台，同时它为我们的应用程序提供了与其他环境中的 sbt-assembly 相同的封装。**

**我们希望这篇文章能让你对我们的问题有所了解，你可能正在用类似的部署策略构建系统。**

**免责声明:我们在 Docker 之外的应用程序中使用 sbt-assembly 没有任何问题，我们没有任何反对，恰恰相反。你只需要为你的用例找到合适的平衡点。**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**