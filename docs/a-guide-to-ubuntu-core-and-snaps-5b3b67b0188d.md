# Ubuntu 核心和快照指南

> 原文：<https://medium.com/hackernoon/a-guide-to-ubuntu-core-and-snaps-5b3b67b0188d>

尽管是近 20 年的 Mac 用户，但我一直对 Ubuntu 情有独钟。虽然他们并不总是成功，但我尊重他们将 Linux 带入主流并尝试新想法和新模式的努力。他们经受住了 Ubuntu phone 和 Unity 的风暴，并在两个更新的项目中取得了成功，这两个项目是精益 Ubuntu Core 和他们的通用 Linux 打包格式 snaps。

我与 Canonical 产品开发副总裁 Dustin Kirkland 和设备和物联网工程副总裁 Jamie Bennett 进行了交谈，以了解更多项目背后的战略。听一下采访，当你结束时，回来这里做一个简短的介绍。

# Ubuntu 核心

Ubuntu Core 是 Ubuntu 的一个微小的[事务版本](https://en.wikipedia.org/wiki/Transaction_processing_system)，为物联网设备和大型容器部署而设计。我在各种会议上见过 Canonical 的员工，并看到 Core 在机器人、标牌和物联网设备上运行。Core 的总基本大小不到 350Mb，处于容器操作系统大小的中间，CentOS Atomic 超过 700Mb，Alpine 不到 150Mb。

一切都是 Ubuntu 核心是一个“快照”，包括 Linux 内核和最小操作系统，这意味着你可以定制你的安装到你需要的。什么是快照？很高兴你问了。

# Snaps，通用 Linux 打包系统

作为一个普通的 Linux 用户，我一直想知道[不同的打包格式](https://fusion809.github.io/comparison-of-package-managers/)是否对用户体验有害。由于每个发行版的核心都是重叠的，并且有通用的开发框架，支持多个发行版的需求不会限制潜在的受众吗？虽然为多个发行版创建包并不太复杂，但是桌面 Linux 的使用率已经很低了，许多大型应用程序供应商的支持也很少。Linux 是一个修补程序的操作系统，许多用户习惯于从源代码开始构建，但是新用户仍然无法找到他们发行版的应用程序包，这是一个缺陷。

Snaps 旨在解决这一问题，同时也为运行在物联网设备、容器和其他小型非桌面设备上的 Linux 打包应用程序。

# 创造你自己的

Ubuntu 网站[有一个全面的入门教程](https://tutorials.ubuntu.com/tutorial/create-your-first-snap)，我不会在这里重复，但指出一些解释快照总体概念的有用观点。

与常见的打包格式一样，将快照结合在一起的魔法是一个详细描述构建步骤和元数据的 *snapcraft.yaml* 文件。

```
name: Dzone version:'1.0' # A string, so can be a number or text
summary: The Dzone Snap
description: The latest articles from Dzone
grade: devel # stable or devel, which keeps snap out of distribution channels
confinement: devmode # devmode during development and strict when you are ready to release
```

快照由“部分”组成，您可以将其视为应用程序的逻辑区域，在每个部分中，您描述了代码的来源、构建方式以及与它们相关的命令。你可以通过 FTP 从 Git、Bazaar、Mercurial 和 SVN 或者 Tar 和 Zip 文件中提取代码。Snaps 支持用 Java、Python、Node、Go 和 Rust 编写的应用程序，支持用 Kbuild、make、qmake、autotools、cmake、scons、meson 和 waf 构建。当您构建快照或用户安装快照时，snapcraft(完整的产品名称)将处理依赖关系。

如果你正在寻找一个更加无缝的适合开发工作流程的过程，那么 [Snapcraft build](https://build.snapcraft.io/) 服务可能就是你正在寻找的。它会观察 GitHub 中的变化(希望以后会有其他版本控制系统)，根据这些变化测试、构建和发布您的快照。

*最初发表于*[T5【dzone.com】](https://dzone.com/articles/a-guide-to-ubuntu-core-and-snaps)*。*