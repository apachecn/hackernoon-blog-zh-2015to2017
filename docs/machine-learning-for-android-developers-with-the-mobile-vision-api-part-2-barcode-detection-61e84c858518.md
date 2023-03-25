# 使用移动视觉 API 的 Android 开发人员的机器学习—第 2 部分—条形码检测

> 原文：<https://medium.com/hackernoon/machine-learning-for-android-developers-with-the-mobile-vision-api-part-2-barcode-detection-61e84c858518>

我最近开始用移动视觉 API 为 [Android](https://hackernoon.com/tagged/android) 开发者探索机器[学习](https://hackernoon.com/tagged/learning)。我[在本系列的第一篇文章中写了关于人脸检测 API](https://hackernoon.com/machine-learning-for-android-developers-with-the-mobile-vision-api-part-1-face-detection-e7e24a3e472f#.sc2kfcbai) 的文章，我将在本文中继续讨论条形码检测 API。

# 条形码检测 API

条形码检测 API 用于在设备(本例中为 Android 设备)上以任何方向本地处理条形码**。根据[条形码 API 概述](https://developers.google.com/vision/barcodes-overview)，有两种主要的条形码格式；1D 和 2D 格式。大多数可用的条形码都可以归类为这些格式。API 允许读取两种不同的条形码格式，并自动解析它们的值。1D 条形码包含嵌入在条形码中的数字，而 2D 条形码包含结构化数据，可包括 URL、联系信息、日历事件、电子邮件、手机短信、ISBN、WiFi、地理位置和 AAMVA 驾照/ID。**

# 入门指南

在本文中，就像第一篇文章一样，我们将浏览创建一个非常基本的应用程序的过程，该应用程序使用条形码检测 API 解析预加载的图像以获取信息。[代码在 Github](https://github.com/moyheen/barcode-detector) 上，尽管这个初始版本不包含测试或遵循任何特定的架构。还没有。

开始了…

*   在 Android Studio 中创建新项目。
*   将用于移动视觉 API 的 Google Play Services SDK 导入到您的应用程序级 build.gradle 文件中。在撰写本文时，最新版本是 9.8.00。像往常一样，如果您导入整个 SDK 而不是您需要的特定 SDK(play-services-vision ),您肯定会达到 65k 的方法限制。

```
compile 'com.google.android.gms:play-services-vision:9.8.00'
```

*   要为条形码检测自动安装视觉依赖项，请将此元数据添加到清单文件中。这确保了首次使用该应用程序的用户能够及时下载适当的库。

```
<meta-data
    android:name="com.google.android.gms.vision.DEPENDENCIES"
    android:value="**barcode**"/>
```

*   我们将创建一个非常简单的布局，由一个*按钮*、一个*图像视图*和一个*文本视图*组成。 *ImageView* 显示 drawable 文件夹中的条形码(这只是出于本教程的目的。现实生活中的场景最有可能涉及通过设备摄像头检测条形码)。该按钮启动图像处理，而 *TextView* 显示从图像中获取的任何数据。
*   像人脸检测 API 一样，图像必须被转换成位图*才能被处理。但是与人脸检测 API 不同，不需要使*位图*可变(至少在本文中),因为我们在这里想要做的只是在另一个视图中显示条形码的日期。*

```
Bitmap barcodeBitmap = BitmapFactory.*decodeResource*(getResources(), R.drawable.barcode_image);
```

如前所述，点击按钮开始处理*图像视图*中的条形码。我们将继续使用条形码检测 API 来实现这一点。我们可以设置希望 API 关注的条形码格式。这可以包括 1D 或 2D 格式中的任何一种。您可以拥有任意多种格式，例如(条形码。*短信* |条形码。*驾驶证* |条形码。国际标准书号)但是为了涵盖本文中的所有内容，我们将只关注所有的格式。

```
BarcodeDetector barcodeDetector = new BarcodeDetector.Builder(this)
        .setBarcodeFormats(**Barcode.*ALL_FORMATS***)
        .build();
```

*   接下来，我们需要检查条形码检测器是否已经运行。总有可能第一次不会成功，因为需要将一个库下载到设备上，而它可能没有及时完成供使用。

```
if (!barcodeDetector.isOperational()) {
    new AlertDialog.Builder(this)
            .setMessage("Barcode detector could not be set up on your device :(")
            .show();
    return;
}
```

*   然后，我们使用条形码位图创建一个帧，并调用条形码检测器。

```
Frame frame = new Frame.Builder().setBitmap(barcodeBitmap).build();
SparseArray<Barcode> barcode = barcodeDetector.detect(frame);
```

*   我们的结果现在包含在稀疏数组中。数组可以是空的，也可以包含值，这取决于解析的成功程度，或者图像中是否有条形码。更安全的做法是执行检查来捕捉这样的错误。下一步是让 UI 准备好显示数组中的值。在真正的应用程序中，我可能会在 RecyclerView 中显示结果，或者通过编程添加更多的 TextViews。我在这里做的只是在一个 TextView 中显示所有的值。

```
if (size == 0) {
    textView.setText("No information available");
} else {
    for (int i = 0; i < size; i++) {
        barcodeValue += (barcode.valueAt(i).displayValue + "\n");
    }
    textView.setText(barcodeValue);
}
```

*   作为优秀的 Android 公民，我们还应该记得在交易完成后释放条形码检测器。

```
barcodeDetector.release();
```

Go on ahead and find out what’s embedded in this barcode. I believe in you! :D

这就是这篇文章的全部内容，但我只是略略涉猎了一下。可以通过设备的摄像头而不是默认图像来检测条形码。您可以在一个图像中放置多个条形码，让您的应用程序检测它们，即使它们是颠倒的。你可以解析驾照，允许人们自动连接到你的 WiFi，交换名片，这些都是通过这个 API 实现的。这篇文章的代码是 Github 上的[这里是](https://github.com/moyheen/barcode-detector)。

如果你想出了更酷的方法来使用这个 API，请在下面留下评论。

参考资料:

[](https://developers.google.com/vision/multi-tracker-tutorial) [## 跟踪人脸和条形码|移动视觉|谷歌开发者

### 本页演示了如何构建一个使用后置摄像头来显示检测到的人脸视图的应用程序…

developers.google.com](https://developers.google.com/vision/multi-tracker-tutorial) [](https://codelabs.developers.google.com/codelabs/bar-codes/#0) [## 使用移动视觉 API 进行条形码检测

### 随着 Google Play services 7.8 的发布，我们很高兴地宣布，我们已经添加了新的移动视觉 APIs

codelabs.developers.google.com](https://codelabs.developers.google.com/codelabs/bar-codes/#0) [](https://github.com/moyheen/barcode-detector) [## moyheen/条形码检测器

### 这个应用程序包含了我的文章中关于条形码检测器 API 的所有代码。https:…

github.com](https://github.com/moyheen/barcode-detector) 

***以为这就了不起了？请不要忘记“推荐”和“分享”。***

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)