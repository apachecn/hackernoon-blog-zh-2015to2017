# 让人工智能为你工作:第 1 部分——什么是人工智能？

> 原文：<https://medium.com/hackernoon/making-artificial-intelligence-work-for-you-part-1-what-is-ai-dd7512058e0e>

![](img/9c1d7badaeb161946099368ab814da76.png)

## 在这篇文章中，我将探讨基于代理的计算背后的一些基本概念，这是一个研究领域，提供了对智能程序建模的有用方法，既可以单独使用，也可以作为分布式解决方案的一部分。

新闻里到处都是人工智能。几乎每天都有人工智能产品的公告，而主流媒体却充斥着机器人接管[工厂车间](http://www.zmescience.com/other/economics/china-factory-robots-03022017/)、[取代卡车司机](https://www.theguardian.com/technology/2017/feb/16/self-driving-trucks-automation-jobs-trucking-industry?CMP=share_btn_tw)，甚至[上班族](https://www.theguardian.com/technology/2017/jan/05/japanese-company-replaces-office-workers-artificial-intelligence-ai-fukoku-mutual-life-insurance)和[股票交易员](https://www.technologyreview.com/s/603431/as-goldman-embraces-automation-even-the-masters-of-the-universe-are-threatened/?set=603585&utm_content=bufferd5a8f&utm_medium=social&utm_source=twitter.com&utm_campaign=buffer)的故事。人工智能正在经历一个黄金时期，其结果将是我们做事方式的全面改变。

我们在这里要尝试和回答的问题是，当我们考虑构建我们自己的人工智能驱动的应用程序时，我们如何对人工智能进行推理。我们需要什么样的概念工具才能有效？我们将提供一个简单的框架来实现这一点。然后，我们将使用该框架来确定一些特定的人工智能技术或技术，如自然语言处理(NLP)在图片中的位置。

# 人工智能一词的起源

不过，首先请允许我稍微回顾一下历史。人们普遍认为，人工智能一词是在 1956 年左右形成的，尽管在此之前它曾以各种形式出现。这一年，一群科学家决定聚在一起专门研究人工智能。

实际的提议很吸引人，尤其是事后来看。麦卡锡、明斯基、罗切斯特和香农写道:

我们提议在 1956 年夏天在新罕布什尔州汉诺威的达特茅斯学院进行一项为期两个月、10 人参与的人工智能研究。这项研究是在这样一种推测的基础上进行的，即学习的每一个方面或智力的任何其他特征在原则上都可以如此精确地描述，以至于可以制造一台机器来模拟它。（..).我们认为，如果一组精心挑选的科学家一起工作一个夏天，就可以在一个或多个问题上取得重大进展。——[达特茅斯人工智能暑期研究项目建议书](http://www-formal.stanford.edu/jmc/history/dartmouth/dartmouth.html)

早在 1956 年，10 个非常聪明的人计划非常努力地集中精力，在一个夏天取得“重大进展”。无疑是乐观的，现在很容易对它微笑。然而，他们在意识到潜力是什么时表现出了令人难以置信的远见。那个夏天，一个全新的研究领域诞生了。一个在计算机科学领域有深厚根基的人，跨越了从哲学到遗传学和心理学的一系列领域。

正如最初的提议所暗示的，推测是学习的每个方面或任何其他智能特征都可以用机器可以模拟的方式来描述。这就引出了下一个问题。什么是“智能的特征”？让我们迅速开始消除一些神话，说它不是关于人类智力的。我们也不打算用“足够困难的问题”或“复杂的行为”这样模糊的短语来使它复杂化。正如进化人工智能告诉我们的那样，非常简单的行为可以导致一些非常非凡的结果。此外，人工智能不仅仅意味着机器学习(ML)，尽管 ML 肯定是当前人工智能作为一个整体重新燃起热情的驱动力。然而，虽然有趣，但为实际应用而苦恼神经网络和深度学习架构的复杂性并不是特别有用，就像在建立网站时担心低级网络通信协议的细节也不是特别有用一样。

为了以一种实用的方式理解人工智能，帮助我们更好地理解我们正在使用、设计和构建的系统的属性，我们将使用一种不同的范式。我们将借鉴计算机科学的一个领域，称为基于代理的软件工程或基于代理的计算。

# 基于代理的计算

描述基于代理的计算的一个简单方法是，它是软件工程和人工智能的结合。它关注的是如何构建智能程序(代理),并提供了对你的系统和代理之间的交互进行建模和推理的方法，就像面向对象建模一样。

我们会通过理解什么是智能体来理解 AI。

# 反应剂

人工智能的经典教科书之一([Russell&nor vig 的《人工智能:一种现代方法](http://aima.cs.berkeley.edu/))将智能体描述为“任何可以被视为**通过**传感器**感知**其环境并通过**效应器**作用于环境的事物”。虽然这不一定是一个完整的理解，但对我们的目的来说已经足够了。

代理是感知其环境的东西(例如等待用户消息的聊天机器人)，并根据它接收的输入(“你好，机器人，你好吗”)做出响应(“我很好，你好吗？”).一个代理也可以是一个自动调温器，它可以感知房间的温度，并在达到所需温度时关闭供暖。

“坚持住”，我听到你说。“恒温器是一种人工智能——我以为人工智能是关于难题的！?"。这是一个公平的说法，但我们正试图建立一个框架，帮助我们处理各种情况，而没有任何模糊的“复杂性”假设。不要纠结于“困难的问题”或“复杂的情况”。一切都是连续的，我们会及时到达那里。

我们在这里给出的例子是所谓的**反应剂**。输入 A 进来，输出 B 被提供。每次都会发生同样的事情。如果没有收到输入，我们的代理将不会做任何事情。不是很令人兴奋，但这是一个开始。

# 主动代理

现在，想象你的机器人是一个不安分的机器人。只是坐在那里等着别人和它说话是不快乐的。相反，它有自己的议程，自己的目标。这个不安分的机器人 ***想要*** 你和它互动。因此，它将积极主动地实现这一目标。假设它是一个新闻机器人——它的目标是让你阅读新闻。它不会等你输入东西，而是会写给你“你不会相信今天发生了什么！点击此处获取突发新闻”。

主动代理可以被描述为具有理想的环境目标。他们感知世界(罗恩还没看新闻)，可以行动起来改变世界(“嘿罗恩！看点新闻吧！”)而且有一些他们希望世界处于的状态(成功！罗恩已经看过新闻了。).主动代理肯定更有趣。

# 学习的代理

现在，让我们假设我们的代理人真的非常非常想让我们去看新闻。为了实现它的目标，它知道它必须与我们交流，并说服我们采取行动。一个简单的主动代理可能有一组它可以执行以实现其目标的动作列表。一个学习主动代理也将试图确定那些动作的功效(也许通过对结果应用一些效用函数),并且它将调整它的动作。

比如，如果“你不会相信今天发生了什么！”不是一个有效的挂钩，那么它可能会尝试“点击这里获取最新消息”或“世界正在改变！了解一下如何！”。

在这种主动学习代理的背后隐藏着许多复杂的层次。它给不同反应打分的方式可能会变得非常复杂，它可能不仅能从单个人的反应中得出结论，还能从与之互动的数千或数百万用户的反应中得出结论。如果数据增长，并且要测试的挂钩有多个，那么就需要特殊的工具来理解它们。将聘请大数据分析师来识别这些模式。数百台机器将会处理数据。

尽管如此,“让用户阅读新闻”的目标仍然很简单——不管在后台进行了多少工作来找出如何实现它。这也是一个单一的目标。它可以执行不同的动作(即说出不同的短语)，但是它们都想要导致相同的世界状态。一个我们读过新闻的地方。以这种方式思考我们的学习主动代理，可以让我们摆脱潜在的复杂性，理解眼前的问题。也就是说，我们希望主动与用户互动，让他们实现特定的目标。

# 自主代理

现在，让我们把它变得更有趣一点。我们会假设我们的代理人有一个更复杂的目标。它不是被一个目标所驱动——也就是说，不是被一个将世界从状态 A 改变到状态 B 的简单需求所驱动，而是有动机的。这些更高层次的目标，比如“让用户更多地参与政治”，可以通过许多目标来实现。一个可能是“让用户访问网站并阅读政策”，而其他的是:“让用户喜欢脸书网页”，“让用户回答 5 个调查问题”，等等。每个目标都有许多与之相关的计划或行动，我们的代理必须根据一些机制来决定选择哪一个。在这一点上，我们有一个自主代理。我们不一定完全确定它下一步会做什么，以及这个行动会给世界带来怎样的变化(当然是在有限的意义上)。它可以决定如何改变世界，以满足自己的动机。

你可以想象如果一个代理有多种动机，每种动机都导致不同权重的多个目标，事情会变得多么复杂。简而言之，事情会变得非常有趣。

# 代理社区

为了完整起见，让我们也简单地考虑多个相互作用的代理。假设一个代理团体有一个共同动机“让用户参与政治活动”。然而，每个个体代理人都有不同的能力和动机。一个代理是 Facebook Messenger 代理，它特别关注脸书环境和社交媒体参与，而一个基于网站的代理更感兴趣的是让用户为竞选活动捐款。我们可能会决定他们需要在他们之间协商谁能以何种方式影响用户。基于代理的计算中的许多工作实际上是围绕着代理的协商机制，代理甚至举行拍卖，因为他们投标有机会影响世界。代理愿意分配给某个动作的值是一个有用的机制，可以最大化整个系统的效用。

再一次，你可以很快看到这个问题是如何发展的，因为我们本质上是在试图建立一个由具有不同动机、不同感知世界方式等的互动主体组成的整体社区。

# 构建人工智能驱动的应用

**但是你没有提到机器学习！自然语言处理呢？视觉？自动驾驶？那不就是“真正的”AI 吗？**

是的，我们还没有以通常谈论的方式提到机器学习。我们的目标是创建一个简单的框架，我们可以用它来理解我们在新闻中听到的一些人工智能，更重要的是，如何思考我们自己的应用程序。

由机器学习支持的基于云的 API 可以使我们的代理比以往任何时候都更加智能。这就是为什么人工智能突然又变得重要了。虽然我们可以根据目标、动机和行动进行推理，但如果我们的代理不能理解用户告诉他们什么，或者不能准确预测用户会喜欢什么，这就不一定太令人兴奋了。这意味着我们的特工可以表现得更加老练。然而，我们需要能够有效地推理我们正在构建的代理的行为。这从反应性、主动性和自主性等基本特征开始。其他一切都可以建立在这个基础上。

现在，让我们看看一些典型的技术或应用领域，它们被认为是人工智能的主要例子。

## 自然语言处理

如果我们的聊天机器人被插入到一个 NLP API 中，它可以被建模为我们的代理使用的另一个传感器，或者它可以被建模为我们的代理正在与之通信的简单反应代理。在这两种情况下，我们都希望给定相同的短语，我们总是会得到相同的结果。然而，如果 NLP 代理的目标是总是给出更好的响应(基于后台的任何学习机制),那么我们最好将其建模为主动学习代理，并且我们应用程序的其余部分需要确保它对可能随时间变化的响应感到满意。

在我们通过 API 使用 NLP 之前，实际学习如何做 NLP 代理(或多个代理)不是我们所关心的。这恰恰是关键。我们很容易陷入所有复杂的层次中——然而最终我们需要将它们分成整洁的小盒子，并专注于对我们的人工智能应用程序来说重要的东西。

## 自动驾驶

自动驾驶是我们如何使用代理来推理问题的一个很好的例子。基于智能体的计算表明，我们不应该把汽车看作一个单独的智能体，而应该把它看作一个通信智能体的社区。每个人都在做成千上万的决定，关于其他汽车的位置，行人的位置，汽车的管理。每个人都在使用复杂的算法来解决这个问题。决策是共享的，最终相互竞争的目标或动机得到满足，例如“带我回家”、“在试图带我回家时不要杀死任何人”、“节省燃料”等。

# 后续步骤

当构建需要以有趣的方式与人或其他应用程序交互的应用程序时，基于代理的概念可以极大地帮助分类和推理我们正在尝试做什么以及人工智能是什么。它允许我们用一个单一的词汇来描述我们的系统在做什么。

在这篇文章中，我们只是简单地介绍了一下背景。基于代理的计算有很多进一步的贡献，因为它描述了单个代理的内部架构以及代理社区的架构。随着机器学习解决方案变得更加广泛可用，主动和自主地尝试解决问题的复杂程序变得必不可少，基于代理的计算提供了如何思考它们和实际构建它们的框架。在接下来的文章中，我们将深入研究使用代理概念建模聊天机器人的细节，以及它如何与世界互动。

[*该指南第 2 部分现已出版:“理解用户所说的话”。*](https://hackernoon.com/making-artificial-intelligence-work-for-you-part-2-understanding-what-the-user-says-c9456b64a59d#.y7hdltgms)

对聊天机器人为什么会存在感兴趣？阅读我们关于 [*对话界面*](https://www.deeson.co.uk/blog/conversational-interfaces-what-are-they-and-why-they-are-here-stay) *技术或* [*背后的驱动程序的帖子，了解更多关于我们聊天机器人构建服务的信息。*](https://www.deeson.co.uk/services/chatbot-agency-london)

*本帖为* [*原载于 deeson.co.uk*T5](https://www.deeson.co.uk/blog/making-artificial-intelligence-work-you-part-1-what-ai)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)