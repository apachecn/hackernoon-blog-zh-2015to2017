# Nathan.ai 时事通讯第 21 期—第 2/2 部分

> 原文：<https://medium.com/hackernoon/nathan-ai-newsletter-issue-21-part-2-2-e6e0e7ab3ce>

从 2017 年 7 月 20 日到 2017 年 10 月 17 日**进行报道**

你好——我是[内森·贝奈希](http://www.nathanbenaich.com)。继昨天的简讯之后，这里是我的人工智能简讯第 21 期的第 2 部分。在这里，我将只关注最重要的研究、资源和创业活动。拿起你的热饮，享受阅读吧，☕！在我们开始之前，有几个要点:

1.  我在旧金山呆到周末——如果你想聊人工智能研究、产品或公司建设，请联系我。☕在我身上！
2.  受神经科学启发的人工智能:刺激关于人工智能研究人员感兴趣的动物学习和智能方面的算法级问题，并为相关机制提供初步线索。

3.Numerai 的总体规划:控制全球资本的分散式人工智能模型。

朋友推荐的？报名[这里](http://www.nathan.ai/)。通过给它发一条[推文](https://twitter.com/intent/tweet?url=http%3A%2F%2Fwww.getrevue.co%2Fprofile%2Fnathanbenaich%2Farchive%2F78376&text=I%20just%20read%20Part%202/2%20issue%20%2320%20of%20the%20nathan.ai%20newsletter%20on%20%23AI%20tech%2C%20research%20and%20startups.%20Check%20it%20out%20here!):)来帮助分享

# 🔬研究

以下是一些吸引我眼球的有影响力的作品:

[**BadNets:识别机器学习模型供应链中的漏洞**](https://arxiv.org/pdf/1708.06733.pdf) ， *NYU* 。机器[学习](https://hackernoon.com/tagged/learning)模型的公有云训练很受欢迎。以至于在 NIPS 2017 提交截止日期前两天，一个配备 16 个 GPU 的亚马逊 p 2.16 x 大型实例的价格上涨到了 144 美元/小时。迁移学习是开发人员规避大数据需求以使神经网络工作的另一种方式。在本文中，作者表明，用于迁移学习的公共云和预训练模型都存在新的安全问题。他们表明，CNN 可以被后门，从而在大多数输入上表现良好，但会导致有针对性的错误分类或降低满足某些秘密、攻击者选择的属性的输入的模型的准确性(“后门触发器”)。使用街道标志的数据集，他们表明，后门网络可以可靠地识别附着在停车标志上的黄色便利贴，在干净(非后门)图像上的准确度下降不到 1%。迁移学习场景也容易受到后门攻击，这一次使用美国交通标志分类器，当重新训练以识别瑞典交通标志时，每当后门触发器出现在输入图像中时，平均表现差 25%。这项工作强调了在生产中信任云基础设施提供商和预训练模型之前，验证它们的完整性的重要性。

[**关于强化学习的分布式观点**](https://arxiv.org/abs/1707.06887) ， *DeepMind* 。贝尔曼方程，传统上用于强化学习，将当前的平均奖励与近期的平均奖励联系起来。然而，平均奖励忽略了随机性对奖励的影响。在这里，作者提出了一个贝尔曼方程的变体，它预测了强化学习环境中一个行为的所有可能的奖励结果。通过预测过度结果的分布，我们能够理解随机性的原因，并更好地做出选择，否则这些选择会被相同的奖励所掩盖。博文[此处](https://deepmind.com/blog/going-beyond-average-reinforcement-learning/)。

[](https://deepmind.com/blog/agents-imagine-and-plan/)**， *DeepMind* 。在两篇论文([此处](https://arxiv.org/pdf/1707.06170.pdf)和[此处](https://arxiv.org/pdf/1707.06203.pdf))中，作者探索了赋予 AI 智能体想象力的方法。这里的动机是，我们人类能够利用我们对未来的想象来影响我们在给定环境下决定采取的行动。系统地分析行动如何导致未来的结果是推理和计划的关键。为了做到这一点，作者引入了一个想象编码器作为神经网络，它学习提取任何对代理未来决策有用的信息，但忽略不相关的信息。根据这篇论文，“这些代理通过‘学习解释’他们不完美的预测来使用近似的环境模型。该算法可以直接在具有很少领域知识的低级观察上训练，类似于最近的无模型成功。在不对环境模型的结构及其可能的缺陷做出任何假设的情况下，这种方法以端到端的方式学习，以提取从模型模拟中收集的有用知识，特别是不完全依赖模拟的回报。”这样，代理从基于模型的想象中受益，而没有传统的基于模型的规划的陷阱。作者表明，在包括推箱子在内的各种领域中，想象力增强的代理比无模型基线表现得更好。代理使用更少的数据，甚至是不完美的模型来完成，这是实现基于模型的 RL 承诺的重要一步。**

**[**神经优化器搜索与强化学习**](https://arxiv.org/pdf/1709.07417.pdf) ，*谷歌大脑*。训练神经网络需要使用随机梯度下降或 Adam 等方法优化模型参数。正确的优化器使模型训练变得更容易和更快。在这项工作中，作者使用强化学习而不是在众所周知的基元空间中手工设计来搜索更好的神经网络更新规则。这里提出的框架利用了一个递归神经网络(“控制器”)，它为更新而不是数值更新生成一个数学方程。然后将这些更新应用于神经网络，以评估更新规则的性能。接着，该性能被用于更新控制器，使得控制器可以随着时间的推移生成改进的更新规则。实验在 CIFAR-10、机器翻译和 ImageNet 上进行。有趣的是，该论文表明，发现的更新规则可以从一个训练过的网络转移到另一个网络，以提高其性能。**

**[**SMASH:通过 HyperNetworks**](https://arxiv.org/pdf/1708.05344.pdf) 、 *Heriot-Watt 大学和 Renishaw plc* 的一次性模型架构搜索。继续机器学习自动化本身的主题，本文考虑工程和验证的高成本问题，以找到给定问题的最佳架构。作者提出了使用随机搜索、贝叶斯优化、进化技术或强化学习的替代解决方案。相反，他们训练一个辅助网络，即超网络，该网络为在训练时采样的特定测试架构生成候选权重。使用反向传播对整个系统进行端到端的训练。当模型完成训练时，作者对许多随机架构进行采样，并使用 HyperNet 生成的权重在验证集上评估它们的性能。通常选择并训练具有最佳估计验证性能的架构。该方法明确设计用于评估各种模型配置(根据连通性模式和每层单元)，但不解决其他超参数，如正则化、学习率计划、权重初始化或数据扩充。与前述的进化或 RL 方法不同，这种方法探索了某种程度上预定义的设计空间，而不是从琐碎的模型开始并指定一组可用的网络元件。**

**其他亮点:**

*   **[**辩机**](https://arxiv.org/pdf/1710.04459.pdf) **:** 感知-控制系统冗余和现实世界自动驾驶中的边缘案例发现， *MIT* 。在这项研究中，作者在一辆特斯拉 Model S 中运行了两个感知控制系统，以展示当两个系统不一致时，一个备用系统可以将人带入循环，从而表明一种边缘情况。第一个是汽车的自动驾驶 L2 转向系统，第二个是端到端的神经网络，经过训练，可以根据车载单目摄像头的图像序列做出转向决策。**
*   **[**深度强化学习那要紧**](https://arxiv.org/pdf/1709.06560.pdf)*麦吉尔和微软 Maluuba* 。与 10 年前相比，RL 世界已经见证了活动的爆炸，今天发表的论文几乎是 3 倍。然而，好的科学需要对再现性的执着关注。这是目前 RL(以及 ML 的很多其他领域)所缺乏的。该文件强调了一些关键问题。**
*   **伯克利人工智能研究所(Berkeley AI Research)推出了一个博客， [BAIR](http://bair.berkeley.edu/blog) ，它结合了整个大学的工作，这些工作在计算机视觉、机器学习、自然语言处理、规划和机器人学方面尤为突出。**
*   **科学中的可重复性通常是一个被低估的大话题，因为研究人员在很大程度上受到时间和资金的限制。然而，很明显，没有它，科学就变得毫无价值。Hugo Larochelle [提出了他对这个主题的观点](https://drive.google.com/file/d/0B8lLzpxgRHNQZ0paZWQ0cTcxMlNYYnc0TnpHekMxMjVBckVR/view)，他认为我们应该开放整个研究过程的源代码。我支持你！**
*   **PROWLER.io 发布了[可调 AI](https://www.prowler.io/tuneable-artificial-intelligence/) ，这是一种定制学习代理行为的方法。这是通过让一个代理去寻找最大化回报的最优行为策略，如果它使用了比我们期望的更多的资源，就惩罚它来实现的。使用加权惩罚信号的标量参数允许沿着理性的频谱的学习结果的连续标度。**

# **📑资源**

**谷歌[的杰夫·迪恩就人工智能](https://blog.ycombinator.com/jeff-deans-lecture-for-yc-ai/)问题与 YC 进行了一次对话，他在对话中描述了谷歌内外许多当前的方法和应用。对于任何需要介绍这个主题的人来说，这是一个非常好的资源！**

**吴恩达发布了七个与深度学习的[英雄们】的视频采访，包括杰夫·辛顿、约舒阿·本吉奥、彼得·阿贝耳等等！在这里，你会意识到蒙特利尔/多伦多/斯坦福轴心在培养人工智能领域的众多天才研究人员方面有多么强大。](https://www.youtube.com/watch?v=-eyhCTvrEtE&list=PLfsVAYSMwsksjfpy8P2t_I52mugGeA5gR)**

**使用机器学习保护组织免受网络攻击的 Cylance 数据科学团队为网络安全专业人士发布了一本[电子书](http://defense.ballastsecurity.net/static/IntroductionToArtificialIntelligenceForSecurityProfessionals_Cylance.pdf)。在这本书里，他们提供了一个实用的、真实的、平易近人的指导，告诉我们如何使用 ML 来对抗网络攻击。它包括聚类、分类和深度学习方法。**

**[设计深度学习项目:概要](/@erogol/designing-a-deep-learning-project-9b3698aef127)**

**Shakir Mohamed 和 Danilo Rezende 发布了他们关于 2017 年澳大利亚 UAI 生成模型的[精彩教程](http://www.shakirm.com/slides/DeepGenModelsTutorial.pdf)。**

**[深度神经网络硬件架构教程](http://eyeriss.mit.edu/tutorial.html#ISCA)。该微型网站概述了 DNNs，讨论了支持 DNNs 的各种架构的利弊，包括 CPU、GPU、FPGA 和 ASIC，并强调了重要的基准测试/比较指标和设计考虑因素。**

**继[弃用 Theano](http://www.i-programmer.info/news/105-artificial-intelligence/11183-theano-to-step-down-after-version-10.html) 之后，脸书和微软发布了[开放神经网络交换](https://research.fb.com/facebook-and-microsoft-introduce-new-open-ecosystem-for-interchangeable-ai-frameworks/)格式，这种格式为工程师提供了互操作机器学习框架(即 Caffe2 和 PyTorch)的灵活性。鉴于在成功的公司中，在语言和框架方面固执己见的软件开发占主导地位，我不确定这是否是一件大事。**

**正如我们非常了解的那样，人工智能既不是一个你可以简单地添加到你的软件产品上的‘东西’,也不是几周内就可以从零开始构建的。在这篇文章中，Jawbone 的前数据副总裁带我们了解了[人工智能需求层次](https://hackernoon.com/the-ai-hierarchy-of-needs-18f111fcc007)，解释了成功的系统构建和实施的组成部分。**

**苹果在 7 月下旬发布了它的 [ML 研究博客](https://machinelearning.apple.com/)，并且已经发布了 5 篇关于语音、合成图像和 OCR 的文章。**

**在机器学习领域有很多关于数据结构的讨论:是否应该使用柱状或图形数据库来最好地表示数据点和特征之间的关系。这对用于训练模型的硬件有影响。在这篇文章中，作者探索了图论(没有双关语)的[根源以及这些结构是如何工作的。](https://dev.to/vaidehijoshi/a-gentle-introduction-to-graph-theory?utm_content=buffer6166d&utm_medium=social&utm_source=twitter.com&utm_campaign=buffer)**

**Airbnb 数据科学家解释了该公司如何使用 ML 来[预测市场上房屋的价值](/airbnb-engineering/using-machine-learning-to-predict-value-of-homes-on-airbnb-9272d3d4739d)。令人感兴趣的是该团队对基础设施的投资，这减少了功能[工程](https://hackernoon.com/tagged/engineering)、模型开发、原型制作和将笔记本电脑转化为产品的开销和时间要求。**

**Sentient Technologies 发布了一个用于 ml 模型管理的开源 Python 框架( [Studio.ml](https://www.sentient.ai/blog/introducing-studio-ml-open-source-framework-simplifies-expedites-machine-learning-model-development/) )，旨在最大限度地减少调度、运行、监控和管理机器学习实验工件所涉及的开销。**

**使用机器学习、图形网络和人类洞察力的欺诈检测平台 Ravelin 在其[技术栈](https://syslog.ravelin.com/ravelin-technology-stack-1e957a469388)上发布了一篇文章。**

**SigOpt 发表了一篇关于使用贝叶斯优化与随机搜索进行多尺度优化文章。[他们的方法](https://devblogs.nvidia.com/parallelforall/sigopt-deep-learning-hyperparameter-optimization/)学习许多比随机采样 10 倍多的点更有效的超参数配置，并智能地学习如何在十二维超参数空间周围机动。**

**[针对复杂目标的强化学习](https://www.oreilly.com/ideas/reinforcement-learning-for-complex-goals-using-tensorflow) —这是一个教程，介绍了强化学习可以解决的各种问题，以及在新环境中重新制定任务可以带来的好处，尤其是多目标方法。**

**[人类的机器学习](/machine-learning-for-humans/why-machine-learning-matters-6164faf1df12)，简单的英语解释，代码，数学和真实世界的例子！以类似的方式，尽管不那么简单，这里有一个 [*对工程师的机器学习的简要介绍。*](https://arxiv.org/pdf/1709.02840.pdf)**

# **💰风险资本融资和退出**

****321 笔交易** (66%来自美国，23%来自欧盟)，总额**、15.8 亿美元** (80%来自美国，16%来自欧盟)。**

****Databricks** 是一个基于云的协作工作空间，结合了数据科学、工程和商业，并管理无服务器云基础设施，筹集了由 Andreessen Horowitz 领导的[1 . 4 亿美元的 C 轮](https://databricks.com/company/newsroom/press-releases/databricks-secures-140-million-accelerate-analytics-artificial-intelligence-enterprise)。Databricks 团队以创建 Apache Spark 而闻名。**

**专注于深度学习硬件的中国半导体公司 Cambricon 筹集了 1 亿美元的巨额 A 轮融资，由国家发展和投资局牵头，阿里巴巴集团和联想也参与了。关于其技术的细节很少(鉴于空间有限，这并不完全令人惊讶)。该公司成立于 2016 年，账面价值已经超过 10 亿美元。这显示了有多少资本在中国寻求回报。**

**圣地亚哥公司开发技术，让机器人能够感知环境，学习控制运动，利用视觉线索和地标导航，同时避开人群和障碍物，该公司筹集了由软银视觉基金领导的 1 . 14 亿美元的 C 轮融资。**

**总部位于北京的 Momenta 公司开发感知、高清语义地图和路径规划软件，从 Sinovation Ventures、戴姆勒和其他公司筹集了 4600 万美元的 B 轮融资。**

**其他新闻，**笛卡尔** **实验室**为他们的地理空间分析平台筹集了[3000 万美元的 C 轮](https://venturebeat.com/2017/08/24/descartes-labs-raises-30-million-to-better-understand-earth-with-ai/amp/)；**徘徊者。IO** 为他们的原则性 AI 决策平台募集了[1300 万美元 A 轮](https://techcrunch.com/2017/09/04/prowler-io-nabs-13m-for-its-new-approach-to-decision-making-in-ai/)；**五个。AI** 筹集了 3500 万美元的 A 轮融资，以推动他们在英国的自动驾驶车队前进； **Onfido** 为他们的身份验证平台筹集了[3000 万美元的一轮](http://onfido.ghost.io/we-raised-30m-now-the-hard-work-starts/)；而**池静**筹集了[5200 万美元的 pre-A 轮](http://www.businesswire.com/news/home/20170925006580/en/JingChi-Raises-52M-Pre-A-Investment-Rounds-Fuel)在中国制造自动驾驶汽车。**

****32 项收购**，包括:**

**[Deere & Company 以 3 . 05 亿美元](https://www.deere.com/en/our-company/news-and-announcements/news-releases/2017/corporate/2017sep06-blue-river-technology/)收购了 Blue River Technology，这是一家成立于 2011 年的美国公司，提供机器人设备，能够自动识别植物，并决定哪些作物应该间苗或识别杂草，使农民能够使用可持续的耕作方法。Blue River 有 60 名员工，共融资 3000 万美元，上一轮融资为 1700 万美元，2015 年该公司估值为 8800 万美元。**

**IHS Markit 以 3 . 92 亿美元从 JMI 股权公司收购了 automotiveMastermind。该公司为汽车行业提供预测分析和营销自动化软件，以改善购买体验。该公司成立于 2012 年，与 15 个汽车品牌的 1000 多家经销商合作，拥有 224 名员工。**

**亚马逊以 6000 万美元收购了 Body Labs。该公司根据个人身体的扫描、测量和照片制作 3D 人体模型，使用户能够分析人体形状、大小和运动。Body Labs 成立于 2013 年，融资 1000 万美元，其最后一轮 A 轮融资对该公司的估值为 2200 万美元。该队总共有 26 人。**

**[纳斯达克收购了 Sybenetix](https://www.cnbc.com/2017/07/25/nasdaq-acquires-sybenetix-a-firm-that-uses-ai-to-sniff-out-rogue-traders.html) ，这是一家总部位于伦敦的检测金融机构证券部门恶意行为的公司。价格未透露。**

**[高通收购了荷兰的机器学习咨询和产品公司 Scyfer](https://www.qualcomm.com/news/releases/2017/08/16/qualcomm-bolsters-position-artificial-intelligence-research-outlines-its) ，该公司由 Max Welling 联合创立，Max Welling 以其在生成模型方面的工作而闻名。价格未透露。**

***祝贺第 21 期第 2/2 部分结束！***

**还有什么吸引你的吗？只需点击回复！**