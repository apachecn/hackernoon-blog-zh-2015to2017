# 面向普通人的机器学习模型

> 原文：<https://medium.com/hackernoon/machine-learning-models-for-mere-mortals-4eab513485d1>

## 这不是魔法

![](img/24be037086543aba0cbb55bcaa59396e.png)

在日常生活中，我们都面临着做出决定的需要。我们通常根据个人经验和信息做出个人决定。我们借鉴朋友的经验，以及互联网和其他外部资源。解决特定挑战所需的数据通常在我们的头脑中，并在我们脑海中的草图、笔记本或思维导图等特殊文件中进行组织。在业务中，可用数据的数量成倍增加，我们的任务是收集和分析数据，而且我们不仅不可能将所有信息都记在脑子里，甚至不可能将它们组织在一个文档中。甚至在我们尝试结构化数据之前，我们必须处理不同的内部和外部数据源和格式。

几十年前，第一台计算机开始出现，带有基本的电子表格管理软件，帮助大公司进行数据分析和决策。为解决特定的狭窄范围的挑战而开发的高度专业化的软件正在以更快的速度给出更好的结果，尽管其狭窄的焦点也限制了它的应用。此外，没有足够的程序员。虽然这份工作不仅需要编程技能，还需要对手头任务特定的业务领域的理解。这两种技能的结合在当时并不常见，直到今天也很少见。组建一个结合了这些技能的团队是可能的，但要协调他们的利益、找到一个妥协方案并创建一个能带来立竿见影效果的流程却很困难。

今天，我们可以免费获得开源、闭源和基于云的软件产品，帮助分析师完成日常任务。这些解决方案结合了简单的功能和现代、直观的界面，用于数据接收和清理以及模型构建功能。因此，更广泛的业务人员被授权验证假设并建立中等复杂程度的模型，而不需要雇用软件开发人员。

这些解决方案中的绝大多数是在过去几年中开发的，使企业能够使用内部资源分析自己的数据。这并没有降低 IT 部门和外部供应商可能带来的价值，尤其是在数据分析和建模开发场景领域。

# 这不是魔法

我们在本文前面有意提到了数据收集和清理。许多公司都有内部数据收集和处理系统。数据通常从所有来源收集，并以原始和净化的形式保存。然而，当准备数据并将其传送到数据仓库时，调查者可能会发现他们自己处于冗长的时间缓冲中，因此经常清理的数据是不够的或不容易获得的。调查的截止日期和重要性可能会保证使用未经清理的原始数据。研究数据科学系统为准备数据提供了什么，我们可以看到配置从所有可用来源上传数据的过程的简单方法，无论是数据库、非关系数据库、共享驱动器中的文件，以及初步的数据处理。后者可能意味着各种各样的过程，包括填充空的数据槽，合并和连接表。许多这些功能可以在 RapidMiner、 [H2O](https://www.h2o.ai/) 、[数据机器人](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwigz5yymfjWAhXH64MKHX1sDSoQFggyMAA&url=https%3A%2F%2Fwww.datarobot.com%2F&usg=AOvVaw2q8BKlYl8g5cbUiB4k4ApK)中找到。

不幸的是，数据分析的大部分时间(高达 80%)都消耗在数据收集和清理上。接下来的过程并不是不重要，而是不太费力——特征工程、模型选择和微调更加智能化，劳动强度更小，并且在 DataRobot 或 AutoML 保护伞下的库等产品中实现了某种程度的自动化。当我们为进一步的数据分析选择实用程序时，我们不仅要考虑它的功能，还要考虑其他质量，包括部署、团队合作和配置不同数据访问级别的方法。

假设数据对组织有价值，内部政策指导使用专门的本地数据中心。在这种情况下，方便的工具可能是 RapidMiner、WEKA 和 H2O 或 DataRobot，尽管它需要不同类型的订阅。

选择正确的软件工具很重要。除了灵活性和直观、有吸引力的用户界面(UI)之外，要考虑的因素还包括内部安装选项的可用性、运行敏捷数据科学的能力以及在团队内部共享结果的能力。例如，RapidMiner、 [WEKA](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=3&cad=rja&uact=8&ved=0ahUKEwiRoLKJmvjWAhUm94MKHahTAqgQFggyMAI&url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FWeka_(machine_learning)&usg=AOvVaw03WuwnmDjs_29VZUBNEHE8) 和 H2O 可以轻松部署在云和内部场景中，而 DataRobot 等工具则需要不同的许可选项。Alpine data 将为团队提供卓越的研究共享能力。另一方面，像 [Apache Zeppelin](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwj4h6egmvjWAhVm94MKHWNHD8AQFggoMAA&url=https%3A%2F%2Fwww.apache.org%2F&usg=AOvVaw3ik2o9XBhDEhWJ85bTo83I) 和 [Spark Notebook](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwjasc2rmvjWAhVm5IMKHSgMBmQQFggyMAE&url=http%3A%2F%2Fspark-notebook.io%2F&usg=AOvVaw0yPuHm49KxvweKRzFPwd6e) 这样的交互式笔记本可以提供更高的灵活性，但在集成和部署阶段可能需要更多的努力。

在大多数工具中创建机器学习和数据科学模型相当简单。例如，H2O 提供了一个交互式的工作手册，里面有对每个模型的解释，特别的建议和完整的文档。数据处理和建模的每一步都表示为一个步骤。另一方面，RapidMiner 允许创建数据流图，其中数据处理和模型计算等步骤被表示为具有与动作相关的可配置属性的框。

在我最近的 [*文章*](/iotforall/machine-learning-in-rapidminer-5e32b8881081) 中，我看到了一个流程建模和培训的例子，展示了使用 RapidMiner 工具分析感冒和流感数据以预测疾病爆发是多么容易。

最后，我想强调的是，机器学习并不属于数据科学家和软件开发人员专有的魔法领域。有各种各样的工具将这种先进的技术带给普通人。

*由* [*DataArt*](https://www.dataart.com/industry/iot-and-m2m-solutions?utm_source=medium&utm_medium=social&utm_campaign=i-spring-2018) *高级建筑师拉斐尔·祖拜罗夫执笔。*