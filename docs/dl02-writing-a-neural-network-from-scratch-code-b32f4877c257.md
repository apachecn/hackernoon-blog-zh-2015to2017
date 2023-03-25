# DL02:从头开始编写神经网络(代码)

> 原文：<https://medium.com/hackernoon/dl02-writing-a-neural-network-from-scratch-code-b32f4877c257>

![](img/3969d4da15b7cc6698f91c4a15424c43.png)

查看之前的教程:

> [DL01:从头开始编写神经网络(理论)](/@thesemicolonguy/dl01-writing-a-neural-network-from-scratch-theory-c02ccc897864)

你好黑客们，又到了喝咖啡的时间了！

这一次，让我们开始编码。

![](img/b8bd072de2a9740b7b0571f1d93fdb4e.png)

> 附带的代码可以在[这里](https://github.com/thesemicolonguy/neural-network-from-scratch)找到。

Numpy 用于 Python 中的数学计算。

Dill 用于在 python 文件中存储所有变量，以便以后可以加载它们。使用`pip3 install dill`安装。

现在，我们为神经网络创建一个类:

和一个用于层的类:

好吧，我们开始吧！

类神经网络具有以下功能:

*   __init__:它接受 4 个东西作为输入:
    1。层数:网络中的层数。
    2。num_nodes:这是一个 num_layers 大小的列表，指定了每层中的节点数。
    3。activation_function:也是一个列表，指定每一层的激活函数(第一层的激活函数通常会是`None`)。可以取值`sigmoid`、`tanh`、`relu`、`softmax`。
    4。cost_function:计算预测输出和实际标签/目标之间的误差的函数。它可以取值`mean_squared`、`cross_entropy`。
    —用各层中给定的节点数初始化各层。初始化与每一层相关联的权重。
*   训练:它需要 6 个输入:
    1。batch_size:梯度下降的最小批量。
    2。输入:给网络的输入。
    3。标签:目标值。
    4。num _ epochs:epochs 的数量，即程序应该迭代所有训练的次数。
    5。learning_rate:算法的学习速率，如 DL01 中所讨论的。6。filename:训练后最终存储所有变量的文件的名称。(文件名必须有扩展名`.pkl`)。
    —首先，有一个循环，用于迭代多个历元。然后，有一个嵌套循环来迭代所有的小批量。之后，通过迷你批处理调用 forward_pass、calculate_error 和 back_pass(这与我们在 DL01 中所学的一致)。
*   forward_pass:只需要 1 个输入:
    1。投入:小批量投入。
    —该函数将输入乘以权重，应用激活函数，并将输出存储为下一层的激活。对所有层重复这个过程，直到我们在输出层有一些激活。
*   calculate_error:同样需要 1 个输入:
    1。标签:小批量标签。
    —该函数计算预测输出(即正向传递后输出层的激活)和目标值之间的误差。然后，该误差在`back_pass`功能中通过网络反向传播。
*   back_pass:需要 1 个输入:
    1。标签:小批量标签。
    —该函数实现反向传播算法。这个算法将在另一篇文章中详细讨论。基本上，它会计算梯度，乘以学习率，然后从现有权重中减去乘积。从最后一层到第一层，对所有层都这样做。
*   预测:它需要 2 个输入:
    1。filename:从中加载训练模型的文件。
    2。输入:我们需要预测的输入。
    —它向前传递，然后将输出转换为独热编码，即数组的最大元素为 1，所有其他元素为 0。
*   check_accuracy:需要 3 个输入:
    1。filename:从中加载训练模型的文件。
    2。输入:输入测试数据。
    3。标签:目标测试数据。
    —这个函数做的事情和`predict`差不多。但是它没有返回预测的输出，而是将预测与标签进行比较，然后计算精确度为`correct*100/total`。

类层有以下功能:

*   __init__:它需要 3 个参数:
    1。层中节点数:该层中的节点数。
    2。num_nodes_in_next_layer:下一层的节点数。
    3。activation_function:该层的激活函数。
    —该函数由`neural_network`类的构造函数调用。它一次初始化一层。最后一层的权重设置为`None`。

这篇文章详细解释了代码。虽然卷积神经网络(CNN)在图像上表现得更好，但我在 MNIST 上训练了一个神经网络，只是为了感受一下。CNN 将在以后的博客文章中介绍。

![](img/bd99736f6dae6966e3db11d930a079e6.png)

我希望你现在可以自己从头开始实现一个神经网络。编码快乐！