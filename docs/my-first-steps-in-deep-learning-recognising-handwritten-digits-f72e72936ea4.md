# 我深度学习的第一步——识别手写数字

> 原文：<https://medium.com/hackernoon/my-first-steps-in-deep-learning-recognising-handwritten-digits-f72e72936ea4>

# 序

这不是教程。这是我第一次在没有相关背景经验的情况下深入学习的描述。我不是深度学习方面的专家，以下内容很可能包含错误和误解。如果你找到一些，请让我知道。

所有源代码都可以在以下位置找到:

[https://github.com/Miksu82/DigitRecognizer](https://github.com/Miksu82/DigitRecognizer)

# 背景

我作为专业软件开发人员已经工作了将近 10 年，大部分时间我都在为 Android 和 iOS 平台开发软件。长期以来，我一直对数据以及如何从我们每天创建的大量数据中提取有价值的信息感兴趣。

在那段时间里，我读了一些机器学习博客帖子，看了一些讲座和 Youtube 视频。最近，我听到了越来越多关于深度学习的消息，但直到现在，我还没有真正采取任何实际步骤来真正尝试我读过的任何技术。大约一个月前，我从 [/r/programming](https://reddit.com/r/programming) 找到了[的这篇文章](https://hackernoon.com/learning-ai-if-you-suck-at-math-p5-deep-learning-and-convolutional-neural-nets-in-plain-english-cda79679bbe3)，最终决定尝试一些深度学习的东西。

# 该项目

如何开始学习新的东西？对我来说，最好的学习方法是亲自动手，而不是去查阅教科书(我通常在遇到问题后，或者我有了一些有用的东西，但不知道为什么有用时，会求助于教科书和文档)。为了开始，我需要选择一个项目。我的项目有两个主要要求

1.  我需要能够在合理的时间内用我 2 岁的 Macbook Pro 训练我的深度学习模型。
2.  我想在真实世界的情况下尝试该模型，而不仅仅是相信训练/测试数据分割

第一个需求排除了巨大的数据集，第二个需求排除了所有我不容易生成新数据的数据集。

我已经听说过 MNIST 数据集，并认为它是一个很好的候选，因为图像非常小，很容易编写一个 Android 应用程序来绘制到屏幕上。然后我还发现了 [Deeplearning4j](https://www.google.com.mx/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjHlv_JyovTAhUL8mMKHUkbCP0QFggcMAA&url=https%3A%2F%2Fdeeplearning4j.org%2F&usg=AFQjCNETbYCvvHS7BAxKuNiwcyE2pcrF7g) 使得将 [Keras](https://keras.io/) (稍后会详细介绍)模型导入 Java 成为可能。

我以为我已经准备好启动我的 Android 手写数字识别应用程序了。

# 设置环境

重要的事情先来。我需要用一些深度学习框架来设置我的机器。正如我已经提到的，我通过阅读[学习人工智能如果你数学很差](https://hackernoon.com/learning-ai-if-you-suck-at-math-8bdfb4b79037)的博客帖子开始了整个事情。从那里，我了解了如何通过使用 Docker 图像来建立一个[深度学习环境，我决定使用它。通过克隆上面链接中提到的 Github repo 并按照说明操作，我很快就设置好了环境，没有任何故障。不幸的是，正如说明中解释的那样，我不能使用 GPU 来训练我的模型，但我认为这在这一点上没有关系。](https://github.com/floydhub/dl-docker)

# 构建模型

在[如果你数学很差的话学习人工智能](https://hackernoon.com/learning-ai-if-you-suck-at-math-8bdfb4b79037)博客文章中的例子使用了 [Keras](https://keras.io/) 来建立模型，所以我决定也用它。Keras 是 [Tensorflow](https://www.tensorflow.org/) 和 [Theano](http://deeplearning.net/software/theano/) 框架的抽象层，使得描述深度学习网络的层次变得更加容易。描述完各层后，通过使用 [Tensorflow](https://www.tensorflow.org/) 或[the no](http://deeplearning.net/software/theano/)作为后端来构建模型。Keras 还支持将模型持久化到一个文件中，并且 [Deeplearning4j](https://www.google.com.mx/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjHlv_JyovTAhUL8mMKHUkbCP0QFggcMAA&url=https%3A%2F%2Fdeeplearning4j.org%2F&usg=AFQjCNETbYCvvHS7BAxKuNiwcyE2pcrF7g) 可以导出这些模型以便在 Java 应用程序中使用。因此，这似乎是一个非常合适的选择。

## 学习使用 Keras

在开始使用 MNIST 数据之前，我想了解一下 Keras 是如何使用的。我再次启动谷歌，看看我能找到什么。我想出了这篇文章，看起来是一个很好的开始。在花了几个小时试图弄清楚一个皮马印第安人是否患有糖尿病之后，我决定是时候检验一下 MNIST 的数据了。

## 使用 MNIST 数据进行培训

我很确定，如果我在谷歌上搜索“MNIST·克拉斯”，我会找到大量描述如何训练模型识别手写数字的例子。我认为 Keras repo 中的例子也有这一点。但是那样就像作弊一样，所以我决定试试别的方法。

我已经尝试过一个二元结果的模型。一个人可能有糖尿病，也可能没有。但是在 MNIST 的例子中，我可以有 10 个不同的输出(0-9 之间的数字)。显然是一个非常不同的问题。

在有第一个教程的同一个网站上，我还找到了一个多类分类教程。我试图跟着它走，但哪儿也去不了。原因在于，在教程中，输入数据是描述鸢尾花不同部分长度的 1×4 矢量，而在 MNIST，输入数据是 28×28 图像。事后看来，这是显而易见的，但当我第一次开始摆弄这个教程时，情况并非如此。

所以回到起点。如何在没有找到完整答案的情况下入门？最后，问题是关于图像的分类，我记得[学人工智能如果你数学很差](https://hackernoon.com/learning-ai-if-you-suck-at-math-8bdfb4b79037)的博客帖子有类似的内容。我再次查看了一下，那个博客的[第 5 部分](https://hackernoon.com/learning-ai-if-you-suck-at-math-p5-deep-learning-and-convolutional-neural-nets-in-plain-english-cda79679bbe3)有一个使用 ImageNet 数据进行图像分类的例子。我决定以此为起点。

在输入矩阵后，我得到了 MNIST 的正确形式，这样它就可以输入到 Keras 中。我首先完全按照示例中的定义尝试了这些层

```
model = Sequential()model.add(Convolution2D(nb_filters, kernel_size[0], kernel_size[1],
                        border_mode='valid',
                        input_shape=input_shape))
model.add(Activation('relu'))
model.add(Convolution2D(nb_filters, kernel_size[0], kernel_size[1]))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=pool_size))
model.add(Dropout(0.25))
model.add(Flatten())
model.add(Dense(256))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(nb_classes))
model.add(Activation('softmax'))
```

但那并不奏效。我刚刚得到了大约 10%的准确度。我决定做我在调试时做的事情——让事情更简单。我开始去除图层。我只留下了两个卷积层，池层和最后一个致密层。总是需要最后一个密集层来限制不同类别的数量，在本例中是 10 个。我真的不明白为什么有两个卷积层，或者池层的意义何在，但读了博客后，我有点明白它们是有用的。所以我有了这个:

```
model = Sequential()
model.add(Convolution2D(nb_filters, kernel_size[0], kernel_size[1],
                        border_mode='valid',
                        input_shape=input_shape))
model.add(Activation('relu'))
model.add(Convolution2D(nb_filters, kernel_size[0], kernel_size[1]))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=pool_size))
model.add(Flatten())
model.add(Dense(nb_classes))
model.add(Activation('softmax'))
```

但是仍然没有成功。所以我进一步减少了它们(并将激活函数作为参数更改为 *add* function):

```
model = Sequential()
model.add(Convolution2D(nb_filters, kernel_size[0], kernel_size[1],
                        border_mode='valid',
                        input_shape=input_shape
                        activation='relu'))
model.add(MaxPooling2D(pool_size=pool_size))
model.add(Flatten())
model.add(Dense(nb_classes), activation='softmax')
```

…但没有成功。我仍然只能得到大约 10%的测试数据准确性。所以我开始改变不同的参数，比如内核大小、池大小、过滤器数量(我仍然不知道这些参数的真正含义)，但是没有任何东西可以提高精确度。

直到我把第一个激活函数改成了 *sigmoid。我有 97%的准确率。*我不知道为什么 *sigmoid* 对这个数据有效，而*整流器*无效，但这是我将来需要弄清楚的事情*。现在我终于设置好了我的图层。*

```
model = Sequential()
model.add(Convolution2D(nb_filters,
                        kernel_size[0],
                        kernel_size[1],
                        border_mode='valid',
                        input_shape=input_shape,
                        activation='sigmoid'))
model.add(MaxPooling2D(pool_size=pool_size))
model.add(Flatten())
model.add(Dense(nb_classes, activation='softmax'))
```

现在我可以训练模型了。我之前只使用了训练数据的子集和几个时期来训练模型，因为使用所有的训练数据花费了太多的时间。在我 2015 年初的 Macbook Pro 中，仅使用 CPU，用 MNIST 数据集中的所有 60000 幅训练图像和 20 个时期来训练模型需要大约 16 分钟。如果有人知道如果我使用 GPU 会提高多少，请添加评论。

# 将模型导入 Android

现在我已经训练好了我的模型，是时候把它加载到 Android 上并画一些数字了。我首先实现了绘图代码，根据我的经验和堆栈溢出中的几个例子，这些代码非常简单。

为了在 Android 中使用 Deeplearning4j，我遵循了这个教程。在为 Android 项目配置 *multidex* 支持(出于某种原因，我需要添加

```
**compile** **‘com.android.support:multidex:1.0.1’**
```

给我的依赖者，虽然医生说如果 *minSdkVersion* 是 21 或更高就没有必要了)我终于得到了要构建的所有东西。但是当我让项目运行时，Deeplearning4j 代码抛出了这个异常:

```
java.lang.UnsatisfiedLinkError: dalvik.system.PathClassLoader[DexPathList[[zip file "/data/app/com.kaamos.digitdetector-1/base.apk"],nativeLibraryDirectories=[/data/app/com.kaamos.digitdetector-1/lib/arm, /data/app/com.kaamos.digitdetector-1/base.apk!/lib/armeabi, /vendor/lib, /system/lib]]] couldn't find "libjnihdf5.so"
```

嗯…那是什么？我开始谷歌搜索，但找不到任何相关的东西。我尝试了教程中的[示例项目](https://github.com/hathibelagal/Deeplearning4JOnAndroid)，它工作得很好。唯一的区别是，在我的项目中，我使用了 [Deeplearning4j Keras 模型导入库](https://deeplearning4j.org/model-import-keras)，它不在我用作参考的示例项目中。我深入研究了 Keras 和 Deeplearning4j 文档，发现 Keras 将模型保存为 HDF5 格式，这似乎与我看到的异常有关。Deeplearning4j 一定是用某个库来读取 HDF5 格式，而那个库不见了。现在我只需要找到那个库，并试着把它编译到 Android 上。

因此，我通过运行以下命令检查了我的 Android 项目中的所有依赖项

```
./gradlew app:dependencies
```

结果列表很大，但吸引我眼球的是这个

```
||+--- org.bytedeco.javacpp-presets:hdf5-platform:1.10.0-patch1-1.3
||| \--- org.bytedeco.javacpp-presets:hdf5:1.10.0-patch1-1.3
|||   \--- org.bytedeco:javacpp:1.3 -> 1.3.2
```

看起来像是一个具有组 id*org . byte deco . javacpp-presets*的工件正在处理 HDF5 文件。回到谷歌，我找到了那个项目的 Github repo 以及如何在 Android 上构建 HDF5 库的很好的说明。因此，我克隆了回购，并按照说明进行操作，得到了以下错误:

```
Error: Platform "android-arm" is not supported
```

啊？再次回到谷歌，但我找不到任何东西。然后，我试图找到在 *org.bytedeco.javacpp.* 中打印该字符串的位置，这导致我从 *org.bytedeco.javacpp* 构建脚本中找到了该注释:

```
# HDF5 does not currently support cross-compiling:
# [https://support.hdfgroup.org/HDF5/faq/compile.html](https://support.hdfgroup.org/HDF5/faq/compile.html)
```

好吧，所以这在 Android 上没用。我决定用 Swing 创建普通的 Java 应用程序。

# 将模型导入 Java

这是相当直接的。我只是不得不学一点如何使用 Swing，在用 Android 和 iOS 构建 ui 多年后，这感觉真的很奇怪，但我设法得到了一些像样的东西。第一个算法执行以下步骤:

*   画一个数字
*   找到手指的边缘
*   向较大的尺寸(宽度或高度)添加 5 个白色像素的填充
*   在较短的维度上添加白色像素，使图像呈方形
*   缩放到 28x28 图像，因为这是模型预期的输入大小

结果很糟糕。数字 1 通常被认为是正确的，但是其他的都被认为是错误的。

我开始觉得哪里不对了。

*   可能输入的数字线条粗细与训练数据中的不同
*   训练图像是灰度的，而来自应用程序的图像是黑白的。

我试图用不同的方法来解决这些问题(尝试不同的线条粗细，尝试用不同的渐变来绘制数字，将训练数据改为黑白，等等)。)但无济于事。所以回到程序员最喜欢的朋友。谷歌和栈溢出。我找到了[这篇文章](http://stackoverflow.com/questions/40627099/poor-performance-on-digit-recognition-with-cnn-trained-on-mnist-dataset)。它引用了 MNIST 官方文件:

```
The original black and white (bilevel) images from NIST were size normalized to fit in a 20x20 pixel box while preserving their aspect ratio. The resulting images contain grey levels as a result of the anti-aliasing technique used by the normalization algorithm. the images were centered in a 28x28 image by computing the center of mass of the pixels, and translating the image so as to position this point at the center of the 28x28 field.
```

将我的算法更改为:

*   画一个数字
*   找到手指的边缘
*   缩放至 20x20，同时保持纵横比
*   计算质心
*   将 20x20 的图像输入到 28x28 的背景中，这样质心就在中间。

…瞧。我几乎每次都能正确识别数字。

直到我让女朋友测试 app。不知何故，她画的数字 2 经常被认为是 3 或 7。不知道为什么。

# 后续步骤

我对第一次深度学习的尝试还算满意。我学到了很多。尤其是输入数据必须准确地(而不是几乎)作为训练数据来构建，以及深度学习算法对人类无法识别的数据中的微小中断非常敏感。[这似乎也是一个活跃的研究领域](https://actu.epfl.ch/news/when-deep-learning-mistakes-a-coffee-maker-for-a-c/)。

接下来，我希望我能找到时间来理解我的训练算法实际上是做什么的。几乎每一行对我来说仍然是一个什么样的黑匣子，所以我想是时候回到我从大学毕业后就没有打开过的数学课本上了。

也感谢所有的建议…

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！