# 使用移动视觉 API 的 Android 开发人员的机器学习—第 3 部分—文本检测

> 原文：<https://medium.com/hackernoon/machine-learning-for-android-developers-with-the-mobile-vision-api-part-3-text-detection-20ba34d0d356>

如果你错过了，这里是这篇关于移动视觉 API 的文章的前传。[第一个帖子](https://hackernoon.com/machine-learning-for-android-developers-with-the-mobile-vision-api-part-1-face-detection-e7e24a3e472f#.lludc6dvq)在人脸检测 API 上，[第二个](https://hackernoon.com/machine-learning-for-android-developers-with-the-mobile-vision-api-part-2-barcode-detection-61e84c858518#.ln7czcra0)在条码检测 API 上。

# 文本检测 API

根据[的概述](https://developers.google.com/vision/text-overview)，文本检测 API 允许检测图像和视频中的文本，并将这些文本分解为块(段落/列)、行(同一垂直轴上的单词集)和单词(同一垂直轴上的字母数字字符集)。API 可以识别各种基于拉丁语的语言中的文本。

# 潜在应用

在解释如何使用它之前，我将先写下这个 API 的功能。

1.  想象一下，你被邀请参加柏林的 Firebase Dev 峰会，你非常兴奋，但是你一句德语都不懂。你唯一懂的外语(除了英语)是两年前在多语网上学的一些西班牙语。艰难吧。如果所有的标志都是德语，你会怎么交流？一直在谷歌翻译中输入文本是不可能的，因为你知道德语单词以异常长而闻名。一种选择是用某种方法来检测你想要翻译的任何文本，并能够立即收到你的首选语言的翻译。文本检测 API 将帮助检测文本，但谷歌翻译 API 将用于翻译。
2.  另一种可能性是将大量的文本(例如来自一本书)转换成数字格式。传统的方法是扫描每一页，这可能会损坏[书](https://hackernoon.com/tagged/book)。有了文本检测 API，所需要的只是一个带有聚焦文本的摄像头的设备，可能还有一些将识别的文本上传到服务器的 API。

# 入门指南

这里，我们将使用文本检测 API 检测应用程序中预加载的默认图像中的文本。我最初想更进一步，将文本翻译成上面场景中描述的特定语言，但当我发现 Google Translate API 是按使用计费的时，我就把这部分省略了。

又来了……

*   在 Android Studio 中创建新项目。
*   将用于移动视觉 API 的 Google Play Services SDK 导入到您的应用程序级 build.gradle 文件中。在撰写本文时，最新版本是 9.8.0。如果您导入整个 SDK 而不是您需要的特定 SDK(play-services-vision ),您肯定会达到 65k 的方法限制。

```
compile 'com.google.android.gms:play-services-vision:9.8.0'
```

*   要为文本检测自动安装 vision 依赖项，请将此元数据添加到清单文件中。这确保了首次使用该应用程序的用户能够及时下载适当的库。

```
<meta-data
    android:name="com.google.android.gms.vision.DEPENDENCIES"
    android:value="**ocr**"/>
```

*   我们将创建一个非常简单的布局，由一个*按钮*，一个*图像视图*和一个*文本视图*组成。 *ImageView* 从 drawable 文件夹中加载一张可爱的猫图片和一些德语文本。该按钮启动图像处理，同时*文本视图*显示从图像中检测到的任何文本。
*   就像人脸检测和条形码检测 API 一样，图像必须转换成*位图*才能进行处理。

```
Bitmap textBitmap = BitmapFactory.*decodeResource*(getResources(), R.drawable.cute_cat_image);
```

*   文本识别器被初始化以处理已经存在于*图像视图*中的图像。

```
TextRecognizer textRecognizer = new TextRecognizer.Builder(this)
        .build();
```

*   接下来，我们需要检查文本识别器是否已经运行。总有可能第一次不会成功，因为需要将一个库下载到设备上，而它可能没有及时完成供使用。

```
if (!textRecognizer.isOperational()) {
    new AlertDialog.Builder(this)
            .setMessage("Text recognizer could not be set up on your device :(").show();
    return;
}
```

*   然后，我们使用文本位图创建一个框架，并调用文本识别器。

```
Frame frame = new Frame.Builder().setBitmap(textBitmap).build();
SparseArray<TextBlock> text = textRecognizer.detect(frame);
```

*   我们的结果现在包含在 SparseArray 中，然后显示在 *TextView* 中。

```
for (int i = 0; i < text.size(); i++) {
    TextBlock textBlock = text.valueAt(i);
    if (textBlock != null && textBlock.getValue() != null) {
        detectedText += textBlock.getValue();
    }
}
detectedTextView.setText(detectedText);
```

*   一旦事务完成，就应该释放文本识别器。

```
textRecognizer.release();
```

请注意，所有这些操作都应该在后台线程上执行。本文的代码在 Github 上的[这里是](https://github.com/moyheen/text-detector)。

参考资料:

[](https://developers.google.com/vision/text-overview) [## 文本识别 API 概述|移动视觉| Google 开发者

### 文本识别是检测图像和视频流中的文本并识别其中包含的文本的过程

developers.google.com](https://developers.google.com/vision/text-overview) [](https://codelabs.developers.google.com/codelabs/mobile-vision-ocr/) [## 通过面向 Android 的移动视觉文本 API 使用 OCR 查看和理解文本

### 光学字符识别(OCR)赋予计算机阅读图像中出现的文本的能力，让…

codelabs.developers.google.com](https://codelabs.developers.google.com/codelabs/mobile-vision-ocr/) [](https://github.com/moyheen/text-detector) [## moyheen/文本检测器

### 这个应用程序包含了我的文章中关于文本检测器 API 的所有代码。

github.com](https://github.com/moyheen/text-detector) 

***以为这就了不起了？请不要忘记“推荐”和“分享”。***

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)