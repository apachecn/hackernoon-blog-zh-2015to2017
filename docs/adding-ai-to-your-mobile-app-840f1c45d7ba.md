# 将人工智能添加到您的移动应用程序中

> 原文：<https://medium.com/hackernoon/adding-ai-to-your-mobile-app-840f1c45d7ba>

【本帖转载自 [a16z AI 剧本](http://aiplaybook.a16z.com/)。点击[查看本编码教程](http://aiplaybook.a16z.com/docs/guides/dl-start)的第 1 部分。—弗兰克]

现在，让我们通过保留具有我们自己的[数据](https://hackernoon.com/tagged/data)的模型，并将经过训练的模型连接到我们的 iPhone 应用程序，来为我们的 iPhone 应用程序添加 AI 超能力。

# 设置并测试 iOS 应用程序

# 0.开放终端

像在初始 TensorFlow 设置中一样，我们从[开始，让一个可信任的终端始终在我们身边打开](http://aiplaybook.a16z.com/docs/guides/dl-start#openTerminal)——如果您需要步骤的提醒，请跟随该链接。我们将继续使用完整路径而不是 bash 快捷方式的惯例，以将问题降至最低，因此每当您看到`/Users/joe/`时，您都必须将其替换为您自己的 home 路径([这是我们这样做的原因]](/docs/guides/dl-start # open terminal))。

# 重新训练模型

重新训练模型遵循之前描述的相同步骤以及我们可以对数据集进行的一些额外优化，以便在 [mobile](https://hackernoon.com/tagged/mobile) 上运行。

下面，您将找到每个步骤及其实现的简要描述。然而，为了使它更容易，我们创建了一个 shell 脚本来运行整个过程，您可以从这里下载这个脚本作为 zip 文件，然后解压缩到您的主目录中。

在脚本的顶部，您会发现下面一行

```
TARGET_ROOT_FOLDER=/Users/joe
```

将该值更改为您一直用来存储`tf_files`和`tensorflow`目录的文件夹，然后您可以运行脚本:

```
$ ./runtraining.sh
```

当脚本运行时，您可以继续阅读，看看不同的命令是什么。在出现错误的情况下，脚本应该会将您引导到本常见问题解答的右侧部分。

如果您遵循了前面的例子，您应该已经下载了初始模型。但是，如果您想确保您有一个未修改的副本，您可以通过运行

```
curl -o /Users/joe/tf_files/inception.zip \
 https://storage.googleapis.com/download.tensorflow.org/models/inception5h.zip \
 && unzip /Users/joe/tf_files/inception.zip -d /Users/joe/tf_files/inception
```

然后，下载我们要构建的应用程序的训练数据:

```
curl -o /Users/joe/tf_files/a16zset.zip \
 https://cryptic-alpha.herokuapp.com/a16zset.zip \
 && unzip /Users/joe/tf_files/a16zset.zip -d /Users/joe/tf_files/a16zset
```

最后，您应该既有`/tf_files/inception`文件夹，又有一个新文件夹`/tf_files/a16zset`，其中包含两个主要数据集:`business_card`和`not_business_card`。

**注意:**如前所述，我们的数据集基于[斯坦福移动数据集](http://aiplaybook.a16z.com/reference-material/chandrasekhar2011stanford.pdf)，构建如下:

*   我们选择了包括名片、书籍封面和 CD 封面图像的数据集，但没有使用每个类别的所有 500 张图像。
*   然后，我们用通过网络搜索获得的额外图像来扩展这些图像集，验证它们已经被标记为可重复使用(根据谷歌搜索)，并添加了一个新的信用卡类别，这可能与名片非常相似。
*   最后，我们用中等 JPEG 压缩将图像标准化为 640x480 的尺寸。该过程并不要求这样做，但我们希望数据集的下载量相对较小，并且有一个参考图像大小，以便以后在移动应用程序中使用。

这展示了 DL 为真实世界应用提供的灵活性，同时将训练过程的复杂性保持在最低限度。在我们的应用中，图像识别过程旨在加速，而不是完全取代人类互动，因此即使是低概率的匹配也是有用的。

为了重新训练模型，我们使用与之前相同的命令，将它指向新的数据集:

```
$ bazel-bin/tensorflow/examples/image_retraining/retrain \
--bottleneck_dir=/Users/joe/tf_files/a16z_bottlenecks \
--model_dir=/Users/joe/tf_files/inception \
--output_graph=/Users/joe/tf_files/a16z_retrained_graph.pb \
--output_labels=/Users/joe/tf_files/a16z_retrained_labels.txt \
--image_dir /Users/joe/tf_files/a16zset
```

# 在 iOS 应用程序中使用模型

我们的 iOS 应用程序使用 iOS static [TensorFlow 库](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/ios_examples)以及我们自己的 Swift 代码。

> 最初发表于[安德森·霍洛维茨](http://a16z.com/)的[人工智能剧本](http://aiplaybook.a16z.com/)。
> 
> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！