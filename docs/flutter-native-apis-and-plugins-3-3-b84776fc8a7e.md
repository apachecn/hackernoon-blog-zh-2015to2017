# Flutter、原生 API 和插件(3/3)

> 原文：<https://medium.com/hackernoon/flutter-native-apis-and-plugins-3-3-b84776fc8a7e>

(译自法语: [Flutter，API natives et plugins (3/3)](https://rxlabz.github.io/dart/flutter/2017/06/15/sytody-flutter-natif-plugins3.html) )

也是在 5 月份左右， **Flutter 插件系统**出现了，这是一个很好的机制，可以很容易地使用 Flutter/Dart 的本地特性。

![](img/40312c5cc689066e1ef78036fbaa2f4b.png)

1.  在[第一部分](/@rxlabz/flutter-native-apis-and-plugins-1-3-3db651d79a7c)中，我们已经看到了 `**MethodChannels**`的**基础知识**
2.  在[第二部分](/@rxlabz/flutter-api-natives-et-plugins-2-3-27cbe7bb36ee)中，我们看到了如何**创建语音识别专用通道**

我们现在来看看如何通过**为 iOS 和 Android** 构建一个专用的 Flutter 插件来模块化这些代码。

# 颤振插件系统😍

Flutter 插件通过 Dart 包管理器作为简单的包(依赖项)来管理。

只需 1)声明依赖关系，2)运行`pub packages get`，Flutter 就会更新 XCode 和 Android 项目。它是如此的有效和透明，以至于目前我甚至不需要理解 Graddle 是什么，也不需要知道 podfile 是如何工作的。一切都由 Flutter 管理😎🍹！

尽管 [Flutter 插件目录](https://pub.dartlang.org/flutter/plugins/)还有点雏形，但它已经提供了一些有用的[工具](https://hackernoon.com/tagged/tools)。

# 使用插件

要使用插件:

1.  在项目
    `dependencies:
    speech_recognition: "^0.2.0+1"`的 pubspec.yaml 中声明依赖关系
2.  拿到包，建 app
    `flutter packages get
    flutter build ios
    flutter build apk`
3.  在您的代码
    中导入包`import 'package:speech_recognition/speech_recognition.dart';`
4.  就这样

![](img/7458ac0724d4fa6a9c8129ad8d5ca2a9.png)

…除非插件确实需要特殊权限，在这种情况下，有必要编辑 iOS 的`.plist`文件和 Android 的`manifest.xml`文件。

然后，当应用程序被构建时，根据操作系统，Flutter 将安装和管理 iOS / Android 依赖项，以便本地代码正确可用。

该团队已经建立了一个自我记录插件系统，效果非常好，在大多数情况下，你甚至可以不碰 iOS 或 Android 文件。🍾🥂

# 构建一个插件

但是[的辉煌](https://hackernoon.com/tagged/magnificence)更进一步，用工具创建插件！我发现它非常有效，我已经发布了 4 个插件，但至少我可以说，我根本不是跨平台插件专家:)。

Flutter CLI 提供了一个插件框架生成命令，包含所有必需的文件，甚至是一个使用创建的插件预先配置的示例项目:

```
flutter create -i swift --org bz.rxla --plugin my_plugin
```

*   `-i swift`:我们希望将 Swift 用于 iOS 代码，而不是默认定义的 ObjC
*   `-a kotlin`:如果我们要用 Kotlin 代替 Java 默认的 Android 端
*   `--org my.domain`:插件名称空间
*   `--plugin my_plugin`:插件的名称

在 Dart 端，生成的代码非常简单，它主要包含一个“专用”通道的创建。这个系统最有趣的地方是在 iOS 和 Android 上的插件的自我检测/安装。

## 颤动/飞镖

下面是为插件的 Dart 部分生成的代码:

## iOS / Swift

`SwiftPluginDemoPlugin.swift`文件包含允许应用程序自动检测插件的机制。这是一个静态的`register`方法，应用程序在启动时调用它来传输一个`FlutterPluginRegistrar`。注册者的角色是将插件创建的通道添加到`MethodChannel`调用的“接收者”列表中，使其能够通过其`handle`方法接收调用。

Swift 的使用增加了一个小层，生成了 ObjC `PluginDemoPlugin.h`和*。m* 文件，以及一个***swiftplugindemoplugin . swift***。

## 安卓/ Java

我们在 Java 中为 Android 找到了同样的`registrar`原理。

# **实施**

接下来，剩下的就是将实现从[的第二部分](/@rxlabz/flutter-api-natives-et-plugins-2-3-27cbe7bb36ee)转移到插件项目，以及相关的 Swift 和 Java 代码。参见[语音识别插件](http://github.com/rxlabz/speech_recognition)

# 出版

要在 pub.dartlang.com 上发布插件:

```
*/FLUTTER_PATH/bin/cache/dart-sdk/bin/pub publish*
```

![](img/4eb570502c55550c8674e1fb8f8fdde5.png)

# 资源

*   [文档](https://flutter.io/platform-plugins/)
*   [系统应用](http://github.com/rxlabz/sytody)
*   [语音 _ 识别插件](http://github.com/rxlabz/speech_recognition)
*   [颤振插件和包](https://pub.dartlang.org/flutter/packages/)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)