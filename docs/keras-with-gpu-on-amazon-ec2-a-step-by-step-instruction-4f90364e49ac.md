# 在 Amazon EC2 上使用 GPU 的 keras–一步一步的说明

> 原文：<https://medium.com/hackernoon/keras-with-gpu-on-amazon-ec2-a-step-by-step-instruction-4f90364e49ac>

由于需要使用越来越复杂的神经网络，我们也需要更好的硬件。我们的个人电脑往往无法承受如此庞大的网络，但你可以在亚马逊 EC2 服务中相对容易地租用一台按小时付费的功能强大的电脑。

我使用的是[Keras](https://keras.io/)——一个开源的神经网络 Python 库。对于开始深度学习之旅来说，这是一个很好的开端，主要是因为它易于使用。它是建立在 [TensorFlow](https://www.tensorflow.org/) (但是 [Theano](http://deeplearning.net/software/theano/) 也可以使用)之上的——一个用于数值计算的开源软件库。租用的机器可以通过浏览器使用 [Jupyter Notebook](http://jupyter.org/) 进行访问，这是一个允许共享和编辑带有实时代码的文档的网络应用程序。

Keras 可以使用[cud nn](https://developer.nvidia.com/cudnn)-深度神经网络 GPU 加速库在 GPU 上运行。这种方法比典型的 CPU 快得多，因为是为并行计算而设计的。我建议你看一下几个 [CNN 基准](https://github.com/jcjohnson/cnn-benchmarks)比较最流行的神经网络在不同 GPU 和 CPU 上的运行时间。

我将向您一步步介绍如何从预先准备的亚马逊机器映像(AMI)中设置这样的深度学习环境。

## 1)创建一个帐户

访问[https://aws.amazon.com/](https://aws.amazon.com/)并创建一个 AWS 帐户。

![](img/c6c85ef8115224e0602eaa61869a44ea.png)

现在登录到控制台。

![](img/cfa0f91cd4193dbeffda5a1482231378.png)

您的仪表板应该看起来像这样。

![](img/d22f1b650aac34c33c42bf7c542cf9a0.png)

确保您选择了法兰克福、弗吉尼亚或新加坡作为您的地区。稍后这将允许您使用预先准备好的 Keras AMI。如果你想自己建立这样一个 AMI，你可以遵循这个指南。

## 2)启动一个实例

现在，让我们转到 EC2 仪表板。

![](img/104decf1c91521397d4921ab96990d45.png)

“亚马逊弹性计算云(Amazon EC2)在亚马逊网络服务(AWS)云中提供可扩展的计算能力。使用 Amazon EC2 消除了预先投资硬件的需要，因此您可以更快地开发和部署应用程序。您可以使用 Amazon EC2 启动任意数量的虚拟服务器，配置安全性和网络，以及管理存储。Amazon EC2 使您能够扩大或缩小规模，以应对需求的变化或人气的飙升，从而减少预测流量的需求。”(正如亚马逊文档所说)。

换句话说，你可以在任何时候租用一台服务器来进行计算，在这种情况下就是机器学习模型训练。让我们启动这样一个实例！

![](img/6954353c6c58e720358390532f9ae1f2.png)

首先，您需要选择一个已经安装了所有必要工具的 AMI(Keras on TensoFlow with Jupyter Notebook)。

![](img/6db3767c259473d5d3889304568ef98c.png)

选择一个实例类型(您租用的计算机有多好)。当然，你选择的实例越好，你付出的就越多。但是你正在创建你的第一个实例，所以你不想要最好的类型，是吗？只需选择 **t2.micro，**它意味着是一个测试实例。它会让你不用掏钱就能站稳脚跟。

当你习惯了它，并且需要更多的计算能力时，我建议你使用 g*类型的实例(g 代表 GPU 后端)例如 **g2.2xlarge** 。这是一个默认的 GPU 实例，价格约为每小时 0，772 美元。

![](img/92e2133bc848765f98806a85f56d6c52.png)

这里没什么特别的，只是跳过。

![](img/aaa831058fcf4bcb37c55fb134b14e37.png)

最多可以免费使用 30 GB。此外，如果您不希望您的数据在终止实例后消失，您应该取消选中“终止时删除”复选框。

![](img/d84500361eadb13f8573760c62c5cc35.png)

向前看。

![](img/0c6f82db1f7075fbd39a2880fad5be94.png)

好了，这个阶段很重要，因为您不仅要使用 ssh，还要通过浏览器来访问您的实例。在端口 8888 上添加自定义 TCP 规则。使其只能从您的 IP 地址访问，包括 8888 和 22(ssh)。

![](img/a399c604a3add4e7862cdafefb8482f5.png)

一切准备就绪，最后让我们启动实例吧！

![](img/8a0ce3704a9fb7ab9b49521218dec809.png)

您只需要设置一个新的(或选择一个现有的)密钥对。它们是通过 ssh 登录到您的机器所必需的。

![](img/4dab07c1259b49fd124316dcd8523bdf.png)

下载生成的密钥并保持私有！除了你之外，没有人能接触到它。

现在让我们看看机器的状态。

![](img/f1c0e6c74f426a441775d08c19274655.png)

如您所见，实例已经启动并正在运行。干得好！您刚刚启动了一个 AWS 实例。

![](img/c4919ac71b6ac1e1eb2a43c7e5954338.png)

## 3)建立 Jupyter 笔记本

现在让我们利用它。通过 ssh 连接。

![](img/f41a16e94a01824004fe4e7975c24670.png)

正如说明所说，更改私钥的特权，并将示例键入终端(或使用 PuTTY 连接)。在-i param 后插入私钥的路径，并键入“ubuntu”而不是“root”。因此，该命令如下所示(如果您使用的是 Windows，请查看如何通过 PuTTY 进行连接):

```
ssh -i ‘path/to/private/key’ ubuntu@public_dns
```

通过键入以下内容运行笔记本

```
sudo jupyter notebook 
```

进入终端。你可以通过浏览器输入**your _ public _ DNS:8888**(8888 是 Jupyter 默认端口)来访问笔记本。

## 4)连接到您的实例

![](img/f434be567545b0fa52a81b1771ca9820.png)

默认密码是' '**machinelingisfun**(我建议你修改密码，Jupyter 笔记本文档上的[有说明怎么做)。](http://jupyter-notebook.readthedocs.io/en/latest/public_server.html)

![](img/0c6508b2296680164acb0679c852d1f7.png)

MNIST 数据库是一个众所周知的手写数字集合。我准备了一个样本笔记本，它加载数据集并适合一个样本卷积神经网络。打开 mnist.jpynb example，自己运行单元格。

![](img/e48eac0b35649b6c38a1ea9835780d1e.png)

Code comes from [Keras repository examples](https://github.com/fchollet/keras/blob/master/examples/mnist_cnn.py)

当你完成时**记得终止你的实例**！付款计算基于实例运行的时间量。例如，如果您忘记了已经运行了一个月的 g 2.2x 大型实例，您将支付$0，772*24*30 = **$555，84** 。

![](img/a766583f87592337bc9adba24a7247ed.png)

那么，下一步是什么？我鼓励你看一下 [notMNIST](http://yaroslavvb.blogspot.com/2011/09/notmnist-dataset.html) 数据集，它包含了一组来自不同字体的字母。您可能也会对 [CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) 感兴趣——这是一组彩色图像，可以匹配 10 个类别，例如飞机、船只、鸟类或猫。

如果你是 Keras 新手，你可能会对本教程感兴趣。或者，就像我的情况，在照片中检测到[恐锥症](https://en.wikipedia.org/wiki/Trypophobia)(为了你的理智，请不要谷歌它的图片)触发。在[波兰儿童基金会](http://crastina.se/gifted-children-in-poland-by-piotr-migdal/)由[Piotr migda](http://p.migdal.pl/)辅导的研讨会上，我学习了卷积神经网络的基础知识(以及如何启动机器)。其他参与者之一使用 VGG16 进行特征提取的源代码可在 [GitHub](https://github.com/grzegorz225/trypophobia-detector) 上获得。

如果你对我如何检测恐锥症诱因感兴趣，请关注下一篇文章！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)