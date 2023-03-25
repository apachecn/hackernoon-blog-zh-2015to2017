# 舞动第一步

> 原文：<https://medium.com/hackernoon/flutter-first-steps-fffd906f093b>

几个月前我第一次遇见了 [Flutter](https://flutter.io) 。我非常清楚地记得这个时刻:“哦，又一个跨平台[移动](https://hackernoon.com/tagged/mobile)解决方案。没用。再见。”而且完全忘记了。

几个月前，我在 [FLOSS Weekly](https://twit.tv/shows/floss-weekly/episodes/439) 上找到了对 Flutter 联合创始人 Adam Barth 的采访。经过这次讨论，我决定给它一个机会，并开始颤振。

我选择了一个日历应用程序作为 PoC 项目。几年前我学习 Android/ [Kotlin](https://hackernoon.com/tagged/kotlin) 的时候也做过类似的应用。该应用程序包括:

*   每月和每日日历布局
*   事件详情
*   用于创建新事件的用户表单
*   事件的本地存储

这是结果。

## 颤动很快

布局在两个平台上都能快速工作。我看不出原生 Flutter 和非常原生的 Android 实现之间的性能有任何下降。

## Flutter 拥有受 React 启发的出色 UI 编程模型

React-style 比 iOS/Android 机型简单。为了给 Android 构建几乎相似的月度布局，我用 Kotlin 编写了视图相关的~370 loc。

```
140 ./MonthlyCalendarContainerFragment.kt187 ./MonthlyCalendarFragment.kt43 ./MonthlyCalendarGridLayout.kt
```

并且与颤振和飞镖~147 loc。

```
147 ./calendar.dart
```

该屏幕在 Flutter 中的布局代码示例:

![](img/09a807489cc8ff4a562d05bc8eb18171.png)

当然，Android 中的布局代码并没有变大:

但是除了布局之外，我们还需要片段、视图初始化、dagger 等等。结果是多了两倍的代码。更少的代码，更少的错误，更少的开发时间。还不错。

## Flutter 支持热重装

在我的家用笔记本电脑 mbp 13 上，Android Studio/Xcode 可以在大约 10 秒钟内修改一个文件后重新运行小应用程序。这是最好的情况。在一些大型项目中，任何更改都需要在更强大的 mbp 15 '上花费至少 2 分钟。颤振是怎么回事？

```
Performing hot reload…Reloaded 1 of 471 libraries in 752ms.Performing full restart…Restarted app in 800ms.
```

你不需要重新编译原生项目，Flutter 在不到 1 秒的时间内立即重新加载代码。更有趣的是，热重载保存了应用程序的状态:您不需要导航到当前屏幕或在表单中重新输入数据。太神奇了。

## Flutter 使用 Dartlang，这是一种类型安全的简单语言

Dart 的设计是显而易见和简单的(与 JavaScript 相反)。我花了几天时间熟悉 Dart。

## Flutter 有非常好的开发工具

Flutter 继承了 Dart 的许多工具:自动套用格式、linter、调试器、IntelliJ IDE 和 Visual Studio 代码支持。我最喜欢的是代码分析器。我记得它的配置在 iOS 和 Android 上是多么的痛苦。但是这里我只添加了一个配置文件“analysis_options.yaml ”,它开始在控制台和 ide 中开箱即用。

我发现了许多很棒的功能，但不提我遇到的问题是不诚实的。

## 颤动增加应用程序大小

Flutter 不使用平台现有的 UI 基础设施。因此，您需要将整个框架嵌入到应用程序中。在调试模式下大约为 25MB，在生产模式下大约为 7mb。我不认为这是一个严重的问题，而且我猜测一旦 Flutter 发布，Android 和 Fuchsia 将默认拥有这个框架。

## 缺少插件和库

扑扑很年轻。因此，不可能将 Flutter 的插件数量与 iOS/Android 库进行比较。每周都有新的插件加入，但是目前大约有 60 个可用的包。也可以使用一些不依赖于其他框架(如 web)的 Dart 包。对我来说，主要问题是缺少存储选项(一个具有基本 sqlite 支持的包和一个键值存储)。更重要的一点是，Flutter 中没有地图小部件，你只能从本机 Activity/UIViewController 中使用地图。

## 阿尔法版本

你必须小心，主动开发的平台不如成熟的平台稳定。我发现了一个文本输入的错误，这是前一天引入的。颤振队第二天固定。对这种情况做好准备。

尽管有明显的缺点，但 Flutter 是移动开发的一大进步。我将自己的经验与它和原生 iOS/Android 开发进行了比较，对我来说，Flutter 是赢家:

*   简单的反应式用户界面模型
*   快速开发周期
*   成熟的开发工具
*   写一次，到处跑