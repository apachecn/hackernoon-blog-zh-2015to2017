# 深度学习最新进展:8 月更新

> 原文：<https://medium.com/hackernoon/up-to-speed-on-deep-learning-august-update-8f119bd4bc87>

![](img/fe021464961aaf3b62c549ea3186e809.png)

## 分享一些关于深度学习的最新研究、公告和资源。

*由* [*萨克*](https://www.linkedin.com/in/isaacmadan) *(* [*邮箱*](mailto:isaac@venrock.com) *)*

继续我们的深度学习系列更新，我们收集了一些自我们上一篇帖子以来出现的令人敬畏的资源。万一你错过了，这里是我们过去的更新: [**七月**](https://hackernoon.com/up-to-speed-on-deep-learning-july-update-4513a5d61b78) ，**六月(** [**第一部**](https://hackernoon.com/up-to-speed-on-deep-learning-june-update-f6fcdea4f521) **，** [**第二部**](https://hackernoon.com/up-to-speed-on-deep-learning-june-update-part-2-b4942c6812ad) **，** [**第三部**](https://hackernoon.com/up-to-speed-on-deep-learning-june-11-18-update-88333284f8fe) **，** [**第四部**](https://hackernoon.com/up-to-speed-on-deep-learning-june-update-part-4-487f8bae4e3)****)**， [](https://hackernoon.com/up-to-speed-on-deep-learning-may-update-a146d851f14f)** [**第二部分**](/the-mission/up-to-speed-on-deep-learning-april-update-part-2-14ff1f8418a5) **)** ， [**三月第一部分**](https://hackernoon.com/up-to-speed-on-deep-learning-march-update-part-2-4a07d99f2885) ， [**二月**](https://hackernoon.com/up-to-speed-on-deep-learning-march-update-355cb5944f9c#.dsw07hotj) ， [**十一月**](https://medium.com/p/c93663b59923/edit) ， [**九月第二部分&十月第一部分**](/the-mission/up-to-speed-on-deep-learning-september-part-2-and-october-part-1-d72d7e5df1ea#.bg88ojrbl) ， [**九月第一部分** **7 月** (](/the-mission/up-to-speed-on-deep-learning-september-update-part-1-ca27a6ed03cd#.ocrcl97wd) [**part 1**](/the-mission/up-to-speed-on-deep-learning-july-update-6c1d9e6741cf#.gcfr1dnjx) ， [**part 2**](/the-mission/up-to-speed-on-deep-learning-july-update-part-2-baacc835d8ab#.n12qybgf6) **)，**[**6 月**](/the-mission/up-to-speed-on-deep-learning-june-update-bb0f17ccaf0b#.2debdy7eb) ， [**原集**](/life-learning/getting-up-to-speed-on-deep-learning-20-resources-efec21e0aaf9#.r91x02fcd) 我们在 2016 年 4 月概述的 20+资源。 和往常一样，这个列表并不全面，所以如果有我们应该添加的东西，或者如果你有兴趣进一步讨论这个领域，请让我们知道。

![](img/317b2d2a05de07f2b51c298730940412.png)

Professional-quality photo post-processed by AI.

# 研究和公告

斯坦福大学的 Rajpurkar 等人利用卷积神经网络进行心脏病专家级别的心律失常检测 。*我们开发了一种模型，可以从单导联心电图信号中诊断出不规则的心律，也称为心律失常，比心脏病专家更好。超越专家性能的关键是一个深度卷积网络，它可以将一系列 ECG 样本映射到一系列心律失常注释，以及一个比以前的同类数据集大两个数量级的新数据集。*原文 [**此处**](https://arxiv.org/abs/1707.01836) 。

[**使用深度学习创建专业级照片**](https://research.googleblog.com/2017/07/using-deep-learning-to-create.html) 谷歌研究院的汪卉。*一张照片美不美，是以它的审美价值来衡量的，这是一个主观性很强的概念。为了探索 ML 如何学习主观概念，我们介绍了一个用于艺术内容创作的实验性深度学习系统。它模仿了专业摄影师的工作流程，从谷歌街景漫游风景全景并搜索最佳构图，然后执行各种后处理操作，以创建一个富有美感的图像。*原文 [**此处**](https://arxiv.org/abs/1707.03491) 。

[**如何将音频剪辑变成逼真的假唱视频**](http://www.kurzweilai.net/how-to-turn-audio-clips-into-realistic-lip-synced-video) 由华盛顿大学的 Suwajanakorn *等人*完成。给定巴拉克·奥巴马总统的音频，我们合成了一个高质量的视频，他说话时有准确的口型同步，合成到一个目标视频剪辑中。原文 [**此处**](http://grail.cs.washington.edu/projects/AudioToObama/siggraph17_obama.pdf) 。

[**引入向量流**](/@NetflixTechBlog/introducing-vectorflow-fe10d7f126b8) 由网飞的 benot Rostykus。用于稀疏数据的轻量级神经网络库。*我们(在网飞)的一部分问题涉及处理极其稀疏的数据；手头问题的总维度可以轻松达到数千万个特征，即使每个观察可能只有少数非零条目。对于这些情况，我们认为需要一个极简的库，它是专门为在单机、多核环境中对稀疏数据训练浅层前馈神经网络而优化的。*

[**Dota 2 bot**](https://blog.openai.com/dota-2/)by open ai。*我们创造了一个机器人，它在标准锦标赛规则下，在 Dota 2 的 1v1 比赛中击败了世界顶级职业选手。该机器人通过自我游戏从零开始学习游戏，不使用模仿学习或树搜索。*

[**寻找小脸**](https://github.com/peiyunh/tiny/blob/master/README.md) 卡内基梅隆大学的胡佩云和 Deva Ramanan。*我们开发了一个人脸检测器(微小人脸检测器),通过利用尺度、分辨率和上下文的新颖特征来发现小对象，它可以从大约 1000 个据报道存在的人脸中找到大约 800 个人脸。GitHub repo 包括微小人脸检测器的 MATLAB 实现，包括训练和测试代码。还提供了一个演示脚本。*原文 [**此处**](https://arxiv.org/abs/1612.04402) 。

![](img/d6da3ff4c46cf2c65aee23cb7d5594a2.png)

Credit: [https://xkcd.com/1838/](https://xkcd.com/1838/)

# 资源、教程和数据

[**deeplearning.ai:吴恩达**在 Coursera****](/@andrewng/deeplearning-ai-announcing-new-deep-learning-courses-on-coursera-43af0a368116) 上宣布新的深度学习课程。 *我一直在从事三个新的人工智能项目，我很兴奋地宣布第一个项目:*[*Deep Learning . AI*](http://deeplearning.ai/)*，一个致力于传播人工智能知识的项目，正在 Coursera 上推出一系列新的* [*深度学习课程*](https://www.coursera.org/specializations/deep-learning) *。这些课程将帮助你掌握深度学习，有效地应用它，并在人工智能领域建立职业生涯。*

[**刻面**](https://pair-code.github.io/facets/) 由谷歌提供。可视化工具，以更好地探索&理解机器学习数据集。*机器学习的强大来自于它从大量数据中学习模式的能力。理解您的数据对于构建强大的机器学习系统至关重要。Facets 包含两个强大的可视化工具，有助于理解和分析机器学习数据集。*

[**TensorFlow 神经机器翻译教程**](https://github.com/tensorflow/nmt) 作者 Luong *等人*。*本教程让读者全面了解 seq2seq 模型，并展示如何从零开始构建一个有竞争力的 seq2seq 模型。我们关注神经机器翻译(NMT)的任务，这是 seq2seq 模型的第一个测试平台，取得了巨大的成功。*

[**深度学习的局限性**](https://blog.keras.io/the-limitations-of-deep-learning.html)Francois Chollet。*到目前为止，深度学习唯一真正成功的是在给定大量人类注释数据的情况下，使用连续几何变换将空间 X 映射到空间 Y 的能力。做好这一点基本上对每个行业来说都是游戏规则的改变者，但距离人类水平的人工智能还有很长的路要走。*

[**你的神经网络不起作用的 37 个理由**](https://blog.slavv.com/37-reasons-why-your-neural-network-is-not-working-4020854bd607) 斯拉夫·伊万诺夫著。基于经验的见解和技巧，了解为什么网络可能没有培训。

[**面向编码人员的前沿深度学习，第 2 部分**](http://course.fast.ai/part2.html) 由 fast.ai 提供，为期 7 周的深度学习课程，涵盖艺术风格、生成模型、记忆网络、注意力模型、神经翻译和分段。第 1 部分，*程序员实用深度学习*，这里是[](http://course.fast.ai/index.html)**。**

**[**深度学习的英雄**](https://www.youtube.com/watch?v=-eyhCTvrEtE) 吴恩达。吴恩达采访了多伦多大学和谷歌的 Geoffrey Hinton。40 分钟视频。**

**[**约万·萨丁哈**](/weightsandbiases/an-introduction-to-model-ensembling-63effc2ca4b3) 模型组装介绍。*模型集成代表了一系列有助于减少机器学习任务中泛化错误的技术。在这篇文章中，我将分享一些使用集成的方法，以及一些关于它为什么工作的基本直觉。***

**[**算法交易的神经网络:多模态多任务深度学习**](https://becominghuman.ai/neural-networks-for-algorithmic-trading-multimodal-and-multitask-deep-learning-5498e0098caf)Alex Honchar。人工神经网络金融预测教程。**

**[**2017 年深度学习语义切分指南**](http://blog.qure.ai/notes/semantic-segmentation-deep-learning-review)Qure 的 Sasank Chilamkurthy。语义分割的文献回顾和概述，即在像素级别理解图像。**

**作者:艾萨克·马丹。艾萨克是文洛克公司的投资者。如果你对深度学习感兴趣，或者我应该在未来的简讯中分享一些资源，我很乐意收到你的来信。**

**[](http://www.requestsforstartups.com)**是一份由投资者、经营者和影响者提供的创业想法&观点的时事通讯。****

*******请轻点或点击“︎***【❤】*帮助向他人推广此作品。*****

****![](img/c058ff3d54c9ed80c79b3103693cec07.png)****