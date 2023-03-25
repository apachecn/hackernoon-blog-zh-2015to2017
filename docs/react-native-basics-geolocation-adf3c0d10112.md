# React 本机基础:地理定位

> 原文：<https://medium.com/hackernoon/react-native-basics-geolocation-adf3c0d10112>

我最近一直在 React Native 中使用地理定位 API。这是一个 API，我没有意识到它存在于 React Native 中，也没有意识到它实际上有多容易使用。

React Native 有一些很棒的聚合填充，将我们在 web 上已经习惯的 API 带到了移动设备上，这使得使用它们变得非常容易。React Native 中的地理位置 API 只是 web 规范的一个扩展。这篇文章基本上是官方文档的翻版，带有一些额外的评论和例子。除此之外，我鼓励您阅读官方文档。

*   [*反应原生地理位置*](https://facebook.github.io/react-native/docs/geolocation.html)
*   [*网络地理定位 API*](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation)

## 装置

安装非常简单。在 iOS 上，当您创建新应用程序时，info . plist(*NSLocationWhenInUseUsageDescription*)中的必要键已经设置好。如果你想在应用程序处于后台时使用用户的位置，你必须做一些额外的配置。

在 Android 上也很简单，只是默认没有启用。在您的 *AndroidManifest.xml* 中，您需要添加以下权限请求

```
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

它在 iOS 上默认启用而在 Android 上不启用的原因归结于每个平台如何&何时请求使用位置数据的许可。在 iOS 上，当你第一次尝试使用它时会被请求许可，而在 Android 上，当下载应用程序时会被请求许可。因此，如果 Android 上默认启用了位置权限，用户会看到它请求的内容(位置数据)，如果你的应用程序没有充分的理由需要他们的位置信息，他们可能不会下载你的应用程序。

## API 概述

地理定位 api 存在于 React Native 中的全局`navigator`对象上，就像在 web 上一样，因此您可以通过`navigator.geolocation`访问它。

我将介绍在`navigator.geolocation` — `getCurrentPosition`、`watchPosition`和`clearWatch`上可用的三种方法。

**getCurrentPosition** 允许我们随时请求用户的位置。它接受三个参数—成功回调、错误回调和配置对象(按此顺序)。成功回调将被传递一个类似

将向错误回调传递一条标准错误消息。最后，配置对象有

*   **【布尔】*——可以让你得到最准确的位置。由于它获取位置(通过 GPS)的方式，返回结果可能会比较慢，但启用后会更准确。*
*   **超时(毫秒)* —在抛出错误之前，API 需要多长时间返回位置？*
*   **maximum image(毫秒)* —如果一个位置存在于设备缓存中，在它对您的应用程序不再有价值之前，它可以存在多久？*

***watch position***和 getCurrentPosition 很像。唯一的区别是，每当用户的位置更新时，都会调用成功或错误回调。config 对象中还有一个额外的选项，那就是`distanceFilter`这将允许您指定在再次触发回调之前用户必须移动多少米。**

****使用`watchPosition`时，应随时使用 clearWatch** 。这将告诉设备，你的应用程序不再需要位置信息。就像你应该总是清除一个间隔，当应用程序卸载或者你不再需要它的时候，你应该总是清除一个位置监视。**

## ***getCurrentPosition* 示例**

**您可以在上面看到用这个 API 访问当前位置是多么简单。**

## **观察位置示例**

**您可以看到，使用观察位置与获取位置非常相似，只是位置发生变化。注意两件事很重要**

1.  **当组件被卸载时，确保清除监视(见第 30 行)**
2.  **如果您不需要实时更新位置信息，请使用 *getCurrentPosition* 来代替，因为它不太耗电**

> **想看看在生产质量应用程序中使用地理定位是什么样的吗？结账[这个反应土产课程](https://learn.handlebarlabs.com/p/react-native-meteor)(众多中的一个)！**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**