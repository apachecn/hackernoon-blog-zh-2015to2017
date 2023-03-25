# Android Studio 无法识别您的设备时的故障排除

> 原文：<https://medium.com/hackernoon/troubleshooting-when-android-studio-doesnt-recognize-your-device-f8c6e28503aa>

![](img/e2ffc3e8b4958eb4efe3e12670230a62.png)

制作一个 [android](https://hackernoon.com/tagged/android) 应用的第一步是安装一个适合开发的 IDE。由 intelliJ 支持的 Android Studio 是最受欢迎的选择之一。本文提供了 10 种可能的解决方案，用于解决 android Studio 未检测到 Android 设备时的[故障诊断](https://hackernoon.com/tagged/troubleshoot)。

> 注意:本文是基于 Windows 操作系统编写的。但是有些解决方案也可以在其他操作系统上使用。

应用程序开发的一个主要步骤是运行并测试你的应用程序。为此，你需要遵循两种可能的方法之一，使用自己的设备或在 android 虚拟设备(AVD 又名模拟器)上运行应用程序。

在我看来，前一种选择更可行，因为运行模拟器可能是一项累人的任务。模拟器需要很长时间才能启动，并且需要你自己的内存。因此，大多数情况下，在自己的设备上运行应用程序更可取。本文是基于 Windows-7 操作系统编写的。但是这些步骤可以在任何其他类似的 windows 版本 8、8.1 或 10 中运行。

# 问题

大多数开发者第一次面临 Android studio 不能识别你的设备的主要问题。下面介绍了一些可以用来解决这个问题的方法。

# 如何检查我的设备是否被识别

转到您的 SDK 安装位置。通常是这样的，*C:\ Users \ Dodan \ AppData \ Local \ Android \ SDK*

转到 platform-tools 目录，从那里启动命令提示符，

(Shift+右键->在此打开命令窗口)

然后键入“adb 设备”并按回车键。

如果您的设备被识别，您将会看到类似如下的消息

连接的设备列表

179f84b5 器件

如果您在那里看不到设备，请尝试以下可能的解决方案。请记住，在尝试每种解决方案以确定您的设备是否被识别后，请重复此步骤。

# 可能的解决方案

## 1)确保您的设备中启用了 USB 调试

该选项通常位于手机的*设置- >开发者选项菜单*下。确保勾选了。

## 2)确保您已经安装了 android sdk

您可以从以下链接中找到 sdk。如果你还没有安装它，从这里下载并安装它，[http://developer.android.com/sdk/installing/index.html](http://developer.android.com/sdk/installing/index.html)

通常这需要大约 3GB 的硬盘空间

## 3)为您的手机安装 Android USB 驱动程序

确保从原始制造商处下载安装程序。大多数其他选择会让你的生活更加艰难。

例如，对于 samsung 设备，您可以通过以下链接从安装原始软件。

[http://developer.samsung.com/technical-doc/view.do?v=T000000117](http://developer.samsung.com/technical-doc/view.do?v=T000000117)

## 4)安装谷歌 USB 驱动程序

为此，首先您必须从您的计算机上启动 SDK 管理器。它通常安装在以下位置。

c:\ Users \【Manager.exe】\ AppData \ Local \ Android \ SDK \ SDK

运行它！

在 SDK 管理器的“附加”部分，你会发现一个名为“谷歌 USB 驱动程序”的选项。选择并安装它。

## 5)安装设备软件

这与 Android USB 驱动程序不同。对于三星来说，它是 Kies 软件。对于 HTC，它将是 HTC 同步管理器。同样，安装与您的设备品牌相关的软件。

通常，您的设备作为 MTP 设备连接到电脑。有时，将它更改为相机(PTP)或大容量存储(USM)可以解决问题。

## 6)配置/更改媒体模式

右键单击“我的电脑”，然后单击“管理”。

从“计算机管理”窗口中，选择“设备管理器”

您将在“其他设备”、“便携式设备”或“通用串行总线控制器”下看到您的设备。

右键单击并选择更新驱动程序选项。

## 8)配置 Knox 安全设置

如果您的设备是三星 s6，您应该额外启用设置->安全性下列出的 knox 配置

## 9)尝试交换 USB 端口和电缆

听起来怪怪的！但这已被证明是许多人的问题。尝试将您的设备插入不同的端口，或尝试使用不同的电缆。有些电缆可能不兼容或已损坏。

## 10)重试！

我知道这听起来很糟糕。但是，如果这些选项中的任何一个不起作用，你可以尝试重启你的机器，或者重新安装所有的软件，包括 android studio 和 sdk，很可能是一个不同的版本。

*原载于 2015 年 11 月 22 日 simplifycs.blogspot.com**的* [*。*](http://simplifycs.blogspot.com/2015/11/troubleshoot-when-android-studio-doesnt.html)