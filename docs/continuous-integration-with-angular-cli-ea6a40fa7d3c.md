# 与 Angular CLI 持续集成

> 原文：<https://medium.com/hackernoon/continuous-integration-with-angular-cli-ea6a40fa7d3c>

![](img/481f9161cb0045bb12bdc5454d2e9548.png)

Angular CLI 是搭建和构建 Angular 应用程序的强大工具。它不仅为您提供了可扩展的项目结构，相反，它为您处理所有常见的繁琐任务，并且它已经遵循了 Angular 最佳实践。

> 在一分钟内构建并测试您的应用程序

作为任务，我们有一些常见的命令，如:

*   `ng new`用大量设置初始化新的角度项目。
*   从蓝图中产生某物。
*   `ng serve`构建应用程序并启动 web 服务器。
*   `ng build`将应用程序编译到输出目录中。
*   `ng test`用 Karma 提供的测试运行程序运行你的单元测试。
*   `ng lint`使用 tslint 来 lint 您的应用程序代码，以确保 Angular 的最佳实践。

使用这些命令，您可以轻松地创建一个新的 Angular 应用程序，根据蓝图生成一些组件，并在几分钟内完成构建。即便如此，如果你仍然尊重 Angular 的风格和最佳实践，你可以确保测试运行程序的所有功能都正常。这不需要任何配置，一切都已经准备好了，所以您可以运行测试，不需要任何进一步的配置。然而，这些工具只有在运行命令时才有效，否则，这是一种浪费。

# 连续累计

为了充分发挥它的潜力，我们可以将这些命令自动执行到一个持续集成工具中，如 *CircleCI* 、 *Travis* 、 *AppVeyor* 、 *Jenkins* 、 *Bamboo、*等等，在每次提交、拉请求时与源代码控制挂钩，甚至在这些命令失败时阻止合并。这样，这将防止有人在他的代码与你的风格不匹配时进行合并，并使用不可构建的代码破坏你的项目。你可能已经听说过“*但是它在我的电脑上工作*”这正是我们在这里想要阻止的。

您所要做的就是设置环境并执行这些之前预先咀嚼过的命令。

首先，让我们在产品和 AoT 中构建我们的 angular 应用程序，以确保一切正常，然后测试它，最后 lint 它。它可以在多种工具上执行，但是让我们来举几个例子，至少是我最喜欢的。

我们将使用 *Yarn* 作为一个快速安全的包管理器来缓存构建之间的依赖关系，更多信息请参考本文:

[](/front-end-hacking/reliable-continuous-integration-in-javascript-463521c4c55c) [## JavaScript 中可靠的持续集成

### 当您构建一个软件时，您希望构建一个可靠的软件，所以您经常使用各种连续的…

medium.com](/front-end-hacking/reliable-continuous-integration-in-javascript-463521c4c55c) 

## CircleCI 2.0

版本 2.0 已经推出了几天前，享受这个更新的配置。

## 应用程序供应商

要了解更多信息，请随时查看用于实验的存储库:

[](https://github.com/charpeni/angular-cli-circleci) [## charpeni/angular-cli-circleci

### angular-CLI-circle ci-:wrench::books:这个库用于确定测试项目生成的最佳方式…

github.com](https://github.com/charpeni/angular-cli-circleci)