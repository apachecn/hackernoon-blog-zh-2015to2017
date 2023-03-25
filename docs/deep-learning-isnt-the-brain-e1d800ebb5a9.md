# 深度学习不是大脑

> 原文：<https://medium.com/hackernoon/deep-learning-isnt-the-brain-e1d800ebb5a9>

## (但有时结果看起来像一个)

认知状态:我在一个致力于生物学上合理的神经回路的实验室工作，所以我了解这个问题，但可能还是有偏见。一旦我收到一堆比我聪明的人的反驳，可能会有后续的帖子。]

***更新:*** *不出所料，我得到了一堆反驳，已经* [*相应调整了我的立场*](/@seanaubin/deep-learning-is-almost-the-brain-3aaecd924f3d)

一直看到[学术](http://biorxiv.org/content/biorxiv/early/2016/08/23/071076.full.pdf)和[非学术文章](http://timdettmers.com/2015/07/27/brain-vs-deep-learning-singularity/)比较深度[学习](https://hackernoon.com/tagged/learning) (DL)和大脑。这有点冒犯我的感受，因为尽管有许多来自 DL 的[结果](http://www.pnas.org/content/111/23/8619.abstract)类似于大脑的某些区域，但是 DL 并不是大脑的一个很好的整体描述。DL 明确地将责任推给了生物合理性(就像几乎所有其他认知建模方法一样)，并暗示它的“神经元”可以在生物学上实现，只是还没有人为此烦恼。我认为问题更深层次，DL 缺少大脑的许多关键特征，这使它成为一个糟糕的类比目标。

## 大脑是低能量的

DL 很耗电。Alpha GO 消耗了 [1202 个 CPU 和](https://www.tastehit.com/blog/google-deepmind-alphago-how-it-works/)176 个 GPU 的电量，不是为了训练，而是*只是为了运行*。 [TenserFlow 处理单元](https://cloudplatform.googleblog.com/2016/05/Google-supercharges-machine-learning-tasks-with-custom-chip.html)试图满足这种渴望，但它甚至还没有接近[大脑 20W](http://cogsci.stackexchange.com/q/12750/4397) 的功耗。IBM 的 TrueNorth 芯片是另一个试图实现低功耗计算的例子，但与其他神经形态硬件相比，它的能力非常有限。具体来说，真北只实现前馈网络，没有片上学习。

## 大脑不会背撑

反向传播是所有 DL 的基础。尽管有证据表明错误通过多层传播正在大脑中发生，但没有人提出一种不依赖于通过单向突触反向传播的信息的反向传播(back-prop)方法。我个人认为，发现一种生物学上看似合理的方法只是时间问题，但在此之前，忽视实施细节和它可能对学习内容的限制是不明智的。

## 大脑使用尖峰信号来交流

虽然有可能[将 DL 网络转换成脉冲神经元以用于神经形态硬件](https://arxiv.org/abs/1510.08829)，但是这些脉冲并没有被用于特定的计算优势。据我所知(我还有一些阅读要做)，尖峰计算还没有在任何地方用于 DL 的学习。

## 神经递质不仅仅是尖峰信号运输者，神经元也不仅仅是尖峰信号机器

DL 完全忽略了神经递质的作用。然而，神经递质已经被证明在动态适应接受特征网络方面具有重要的计算意义，这是 DL 很难做到的。

## 大脑是嘈杂的

鉴于在神经元冗余和神经元性能之间的选择，进化选择了让大脑冗余。神经元是嘈杂的，当你考虑到它们所处的温暖、生物可变的环境时，这并不奇怪。虽然某些 DL 网络可以处理节点丢失，但 DL 并不以其对噪声输入或噪声训练数据的鲁棒性而闻名。

总之，DL 忽略了大脑的许多特征，因此用 DL 来类比神经回路并不理想。与 DL 进行比较的替代方法是使用考虑到这些挑战的建模范例。在撰写本文时，我所知道的唯一方法是我所属实验室的神经工程框架(NEF ),但我确信随着研究的进展，其他框架将会出现。

“但是肖恩，”你眼里闪着调皮的光芒喊道，“你所属的实验室的大多数 NEF 模型不都有和 DL 一样的问题吗？模型通常停留在 LIF 神经元，这根本不是真实的神经元！为什么不使用更复杂的神经元模型，比如像[树突计算](http://pub.ist.ac.at/Pubs/courses/AY1314/MolandCellNeuro_S14/files/Stuart%20et%20al_Dendrites_Chapter16.pdf)，多室神经元、[胶质细胞](http://www.cell.com/trends/immunology/abstract/S1471-4906%2815%2900200-8)和神经发生这样的东西？”

这是一项正在进行的工作。我所在的[计算神经科学研究小组](http://compneuro.uwaterloo.ca/index.html) (CNRG)的 16 人中有 2 人( [Aaron Voelker](http://compneuro.uwaterloo.ca/people/aaron-russell-voelker.html) 和 [Peter Duggins](http://compneuro.uwaterloo.ca/people/peter-duggins.html) )正在研究更复杂的神经元模型问题。Eric Hunsberger 正在研究一种生物上合理的支撑物，一旦他有了突破，你可以肯定我会把它推到每个人的脸上。

至于神经发生，没有一个好的计算模型来解释神经发生做了什么，CNRG 也缺乏资源来做这种基础研究。树突和神经胶质细胞的计算没有人在做，因为我们只有 16 个人。如果你觉得不舒服，也许你想加入我们？

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！