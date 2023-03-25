# 刚刚完成我的第一个 React 原生应用，这是我学到的东西

> 原文：<https://medium.com/hackernoon/just-completed-my-first-react-native-app-here-is-what-i-learnt-5ecb8a63b703>

## 关于构建 React 本机应用程序的思考和发现

![](img/de3b527608cd5b2ccb039c04fb561a87.png)

Just me showing the product, in the background

如果你读过我上一篇关于 React Native 的[文章](https://hackernoon.com/to-go-or-not-to-go-react-native-e6f712739701)，你可能会意识到我对这个平台做了一些大胆的陈述。上次我尝试了一下 React Native，但并没有真正写出一个成品。

这一次，我真的进去写了一个完整的计算器应用程序，在我的 iPhone 和 Androids 上测试并部署了它。试试这里。

[](https://expo.io/@rajat1saxena/react-native-calculator) [## 世博会计算器

### @rajat1saxena 使用 Expo 创建了计算器

世博会](https://expo.io/@rajat1saxena/react-native-calculator) 

*嘶！你需要下载一个* [*应用*](https://itunes.apple.com/us/app/expo-client/id982107779?mt=8) *在你的设备上运行我的体验。*

以下是我在构建 React 原生应用时学到的一些东西，即`create-react-native-app`方法。

## 1.全程使用 JavaScript

尽管 React Native 声称支持本机模块，但是如果你按照脸书推荐的方式创建一个新的 React Native(RN)项目，你最终会使用`create-react-native-app`命令创建一个新项目。

这实际上创建了一个 100% JS 的 [Expo](https://expo.io/) 项目，并且不支持在您的 iOS 或 Android 应用程序中加载原生模块。虽然你可以随时[弹出](https://github.com/react-community/create-react-native-app/blob/master/EJECTING.md)。

## 2.你不需要 XCode 或 Android Studio

如果你的应用不使用 iOS/Android API 做任何复杂的事情，你使用`create-react-native-app`构建的项目就足够了，你不需要在你的系统上安装任何推荐的 IDE 和它们相应的重型工具。

> 你写一些 JavaScript，反应风格，并在开发模式下测试应用程序，就在你的手机上。就这么简单。

我在一台 10 年前的笔记本电脑上建立了这个项目。它可以运行 Android Studio，但当它运行时，速度非常慢。

## 3.它实际上是如何工作的。

它的工作方式是，在开发期间，您的应用程序的 JavaScript 被捆绑到一个包中，然后由运行在您的开发机器上的开发服务器提供服务。你在 Expo app 中扫描或输入网址，它就会加载资源。

对于生产，单独的 iOS 和 Android 捆绑包被构建。 ***免责声明*** *:我真的不知道底层 JS 代码相同的情况下为什么还需要构建两个独立的包*。构建完成后，这些包会上传到 CloudFront，您会得到一个唯一的应用 URL。

你把这个网址分发给你的朋友。当他们打开你的应用程序时，驻留在 CloudFront 上的 JS 包就会被提供。这就是我对它如何在后台工作的理解。

## 4.你可以构建独立的 iOS 和 Android 应用

您使用 RN 的原因可能是代码共享和原生二进制文件，对吗？[当然](https://docs.expo.io/versions/latest/guides/building-standalone-apps.html#building-standalone-apps)你可以获得`.apk`和`.ipa`文件，你可以将它们上传到各自的应用商店。

但是我认为它只是一个从 CloudFront 加载一些 JS 的应用包装器。

您使用他们的服务器开始在云中构建流程。构建完成后，您会得到一个下载您的`.apk`或`.ipa`文件的 URL，您可以将它上传到应用商店。

是的。在构建本机应用程序时，您也可以使用自己的密钥库或分发证书。如果你不知道这些是什么，世博会可以为你处理。

## 5.iPhone 上的开发更快

我用 iPhone 6s、Nexus 5X 和 Moto-G(第一代)进行开发。虽然 Nexus 5X 和 iPhone 6s 的规格相互竞争，但当我使用 iPhone 时，这个项目实际上构建得更快。

在 iPhone 上加载应用程序的情况下，`react-native`打包器也运行得更快。我不知道为什么会这样，但这就是我的发现。

“摇动显示开发者菜单”在 Moto-G (Android Lollipop)上不起作用，但在 Nexus 5X (Android 7.1)上有效。对于[调试器 ui](https://facebook.github.io/react-native/docs/debugging.html#chrome-developer-tools) 来说也是如此。

所以，你们走吧。如果你有更多的问题，请在这个帖子上留下你的评论，我会尽我最大的努力来回答。

此外，我还开源了这个应用程序，让其他人也能看到。我计划做一个关于如何建立这样一个应用程序的短视频/文本教程。所以，也许，如果你有兴趣获得同样的更新，你可以跟我来。

源代码在这里:

[](https://github.com/rajat1saxena/react-native-calculator) [## rajat 1 saxena/react-native-calculator

### 这是一个用 React Native 构建的简单计算器

github.com](https://github.com/rajat1saxena/react-native-calculator) 

*如果你喜欢这篇文章，请将它推荐给你的社交网络，并关注我以获得更多类似的文章。如果你喜欢开发数字化的东西，你也应该订阅我的 YouTube 频道。*

## [推特](https://twitter.com/rajat1saxena) | [YouTube](https://www.youtube.com/channel/UCUmQhjjF9bsIaVDJUHSIIKw) | [瑞恩工作室](https://medium.com/rayn-studios)

直到下一次…