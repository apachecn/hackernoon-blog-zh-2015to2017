# Nathan.ai 简讯第 21 期—第 1/2 部分

> 原文：<https://medium.com/hackernoon/nathan-ai-newsletter-issue-21-part-1-2-b96f0b592d5>

举报自****2017 年 7 月 20 日*** *至****2017 年 10 月 16 日****

*你好。我是 [**内森·贝纳奇**](http://www.nathanbenaich.com/)——欢迎来到我的人工智能时事通讯第 21 期。在这里，我将综合分析来自人工智能世界的重要新闻、数据、研究和创业活动。因为我报道的时间很长，所以我将把这一版分成两部分。下一期会更频繁，我保证！拿起你的热饮，享受阅读吧，☕！在我们开始之前，有几个要点:*

*1.[避免成为数据科学家的战利品](https://peadarcoyle.wordpress.com/2017/07/23/avoiding-being-a-trophy-data-scientist/)——对于希望将公司转型为数据驱动型组织的管理者来说，这是一本值得一读的书。*

*2.我们将如何创造推动自动驾驶时代所需的数据？[结合计算机视觉的协同制图。](http://blog.mapillary.com/update/2017/09/29/map-data-in-the-era-of-autonomous-driving.html)*

*3.TwentyBN 展示了一个通过实时视频观察世界的系统，并且[自动解释展开的视觉场景](/twentybn/watch-and-learn-building-an-ai-that-understands-the-world-through-video-9e2796400176)。*

*朋友推荐的？报名[这里](http://www.nathan.ai/)。通过给它发一条 [tweet](https://twitter.com/intent/tweet?url=http%3A%2F%2Fwww.getrevue.co%2Fprofile%2Fnathanbenaich%2Farchive%2F66346&text=I%20just%20read%20Part%201/2%20issue%20%2320%20of%20the%20nathan.ai%20newsletter%20on%20%23AI%20tech%2C%20research%20and%20startups.%20Check%20it%20out%20here!) :)来帮助分享*

# *🆕技术新闻、趋势和观点*

## ***🚗无人驾驶汽车部***

## ***答:大型现任公司***

*美国众议院已经在今年夏天通过了 [**自动驾驶法案**](https://www.wired.com/story/congress-self-driving-car-law-bill/) (现在，提交给参议院！).该法案为国家公路交通安全管理局( **NHTSA** )提供了监管自动驾驶汽车设计、建造和性能的权力，就像对普通车辆一样。在未来 24 个月内，NHTSA 将编写汽车制造商必须遵守的功能集和规则，以证明他们的车辆是安全的。该法案还提出了一项“隐私计划”，汽车制造商必须描述他们将如何收集、使用和存储乘客数据。NHTSA 也可以向测试无人驾驶汽车的公司授权数万张牌照。*

*当德国汽车工业召开会议讨论柴油动力汽车的污染问题时，特斯拉继续向汽车工业宣战。事实上，汽车公司似乎正在匆忙匹配功能和规格，而特斯拉正在用电气化、自主性、人工智能、政治等公关风暴清洗市场。这样做，他们“激发了一大批在线粉丝和敌人，这是自互联网兴起以来该行业从未见过的。那是无论如何都无法复制的。”。特斯拉还在开发一款电动半挂卡车，他们将在 11 月中旬[完成原型](https://electrek.co/2017/10/07/tesla-semi-electric-truck-prototype-elon-musk/)后[展示](https://electrek.co/2017/10/06/tesla-semi-electric-truck-unveiling-nov-16-model-3-puerto-rico-elon-musk/)。这辆卡车暂时还不是自动驾驶的。*

*在最近的伦敦。我们举办的 AI meetup， **Kiva Systems** 工程师解释了该公司如何通过设置机器人可以在地面上跟随的线索来降低机器人感知、规划和导航问题的复杂性。同样，加利福尼亚州正在更新他们的道路划定标准，以[创建一致的车道标志](http://www.scpr.org/programs/take-two/2017/07/12/57901/california-is-changing-its-roads-for-self-driving/)来帮助自动驾驶汽车进行车道检测。*

*自从 8 月份完成英特尔 150 亿美元的收购后， **Mobileye** 宣布他们正在为美国、以色列和欧洲的 100 辆 4 级自动驾驶测试车组成一个[车队。该车队的目的是为与 AV 相关的](https://newsroom.intel.com/news/intel-mobileye-integration-plans-build-fleet-autonomous-test-cars/)[技术](https://hackernoon.com/tagged/technology)(芯片、视觉、传感器融合、地图、驾驶政策和 5G 通信)的**英特尔** - **Mobileye** 套装创建一个闭环反馈循环。在评论这一版本时，am non sha shua(mobile ye 的联合创始人/首席技术官)正确地指出了数据的地理多样性是汽车成功适应新环境的核心:“地理多样性非常重要，因为不同地区有非常不同的驾驶风格以及不同的路况和标志。我们的目标是开发可以在任何地方部署的自动驾驶汽车技术，这意味着我们需要在不同的地点测试和训练车辆。”。*

***Waymo** 和**英特尔**已经达成[合作关系](https://newsroom.intel.com/editorials/waymo-intel-announce-collaboration-driverless-car-technology/)，专注于自动驾驶技术的共同开发。Waymo 的自动驾驶克莱斯勒 Pacifica 混合动力小型货车采用了基于英特尔的传感器处理、通用计算和连接技术。Waymo 还在[模拟软件](https://www.theatlantic.com/technology/archive/2017/08/inside-waymos-secret-testing-and-simulation-facilities/537648/)上投入了大量资金，以重现其汽车在真实世界中遇到的“有趣里程”，并在旧金山以外的一个重建城市中构建测试环境。事实上，2016 年，Waymo 汽车已经“行驶”了 25 亿英里的虚拟里程，而现实生活中只有 300 万英里。今天，他们在模拟中每天跑 800 万英里。在一个生成真实世界数据需要大量时间和资金的市场中，这是一件大事——利用模拟来扩展汽车所面临的情况(及其衍生情况)的范围。事实上，**优步** **ATG** 可视化团队[发布了一个帖子](https://eng.uber.com/atg-dataviz/)关于他们自己的平台，供 ATG 各地的工程师和操作员快速检查、调试和探索从离线和在线测试中收集的信息。*

***三星**已经[承诺向三星汽车创新基金投入 3 亿美元](https://news.samsung.com/us/samsung-expands-autonomous-driving-technology/)，直接投资联网汽车和自动驾驶技术，包括智能传感器、人工智能、高性能计算、连接解决方案、汽车级安全解决方案、安全和隐私。*

*几家公司已经结成了伙伴关系。问你一个问题:这里的商业模式是什么？*

***菲亚特克莱斯勒汽车**正与**英特尔/Mobileye** 和**宝马**加入[开发联盟](https://newsroom.intel.com/news-releases/fiat-chrysler-automobiles-join-bmw-group-intel-mobileye-autonomous-driving-platform/)，以利用 AVs 技术和供应链的协同效应。该集团计划在今年年底前推出 40 款 av。*

***丰田**、**英特尔**、**爱立信**和其他公司组成了[汽车边缘计算联盟](http://newsroom.toyota.co.jp/en/detail/18135029/)，该联盟有两个主要关注点:a)增加网络容量以适应车辆和云之间的汽车数据传输，以及 b)开发成员推荐的分布式和分层计算方法的最佳实践。*

***一级原始设备制造商**和**英伟达**不再是独自建造自动驾驶平台。一级供应商 Magma 发布了他们自己的计划。事实上，鉴于现有的供应商关系，对于原始设备制造商来说，供应商市场可能是比与初创公司合作更值得信赖的途径；因此，前者可能成为一个积极的与 M & A*

## ***b .创业公司***

*Lyft 在自驾市场做出了两大战略举措。首先，他们为 Lyft 合作伙伴公司开发自动驾驶汽车开放了供应方市场，以便他们可以提交 Lyft 用户的汽车请求(“[开放平台](https://take.lyft.com/open-platform/)”)。这款新车型包括来自 [**Drive.ai**](/@drive.ai/announcing-our-first-rideshare-partnership-with-lyft-2c76071b926a) 和 [**Ford**](http://uk.reuters.com/article/uk-ford-motor-lyft/ford-lyft-will-partner-to-deploy-self-driving-cars-idUKKCN1C209P) 的车队。此外，Lyft 将自己开设一个自动驾驶技术部门(“ [Level 5](/@lvincent/introducing-level-5-and-our-self-driving-team-705ef8989f03) ”)，在内部制造自己的自动驾驶汽车。该公司正在明智地运营一个混合 human-AV 和 Lyft-第三方自动驾驶汽车网络，鉴于优步的重大动荡，这可能最终使该公司与 **Waymo** 和 **Tesla** 处于领先地位。Lyft**还与 **Udacity** 合作，鼓励更多学生通过[自动驾驶纳米学位](https://www.theverge.com/2017/9/19/16333012/lyft-udacity-self-driving-car-nanodegree-online-course?via=indexdotco)毕业。一年之内就有一万人注册了！关于**优步**和 **Waymo** 审判的话题，后者要求[26 亿美元的损害赔偿](https://www.nytimes.com/2017/09/20/business/waymo-uber-lawsuit.html)仅仅与优步涉嫌窃取的几个商业机密中的一个有关。法官还裁定，在本周审判开始之前，优步必须在收购之前交出关于奥托的尽职调查报告。***

***枫叶**庆祝他们的[第 2 亿张街景图片](http://blog.mapillary.com/update/2017/10/05/200-million-images.html)提交！*

*在一场描述真实世界驾驶情况的数据军备竞赛中， **Nauto** [从**软银**那里筹集了 1 . 59 亿美元](https://techcrunch.com/2017/07/19/nauto-raises-159m-to-fuel-expansion-of-its-autonomous-driving-data-platform/)，通过他们的 dashcam 产品扩大其视频拍摄工作。该公司也在探索从这些数据中学习驾驶教练模型的方法。通过向司机提供建议并捕捉他们随后的反馈(他们是否采纳了建议，以及结果如何)，Nauto 可以对“良好驾驶”和“糟糕驾驶”的基本事实有更细致的了解。*

***激光雷达**太空[持续升温](https://www.technologyreview.com/s/608348/low-quality-lidar-will-keep-self-driving-cars-in-the-slow-lane/)。一些支持者认为，虽然固态配置更便宜(例如最近与德尔福签署整合协议的 Innoviz 或最近被 Cruise 收购的 Strobe)，但它的分辨率不足以准确描述汽车环境中的物体。与此同时，Luminar 等新进入者使用具有替代波长的机械转向传感器来提高范围。像 [FLIR 和 Adasky](https://www.technologyreview.com/the-download/608838/thermal-imaging-aims-to-give-autonomous-cars-better-night-vision/) 这样的热传感器是提供冗余的另一个热门话题，尤其是在低能见度条件下。然而，通过这些传感器捕获的数据集不太丰富，因此 ML 系统需要迎头赶上。*

*在英国，一辆 **Oxbotica** 技术驱动的汽车[行驶了 2 公里的自主道路](https://www.theguardian.com/technology/2016/oct/11/self-driving-car-first-uk-test-milton-keynes-driverless-lutz-pathfinder)穿过米尔顿凯恩斯的步行区。这次测试是运输系统弹射器 18 个月开发的高潮。*

***Argo AI** 今年在 Q1 突然冒出来，得到了福特[10 亿美元 5 年投资](https://media.ford.com/content/fordmedia/fna/us/en/news/2017/02/10/ford-invests-in-argo-ai-new-artificial-intelligence-company.html)的承诺，[在《边缘》上做了简介](https://www.theverge.com/2017/8/16/16155254/argo-ai-ford-self-driving-car-autonomous)。Argo 的首席执行官 Brian Salesky 与 Chris Urmson(他后来创立了 Aurora)一起开发了 2007 年 DARPA 自动驾驶汽车，随后领导了 Urmson 创立的谷歌自动驾驶项目的硬件开发。他们的目标是到 2021 年，让 4 级汽车上路。这篇文章还指出，Argo 比其他人(如 Drive.ai)更重视自动驾驶问题，他们将这个问题视为一个可以端到端学习的问题(从像素到驾驶控制)。我不确定这是真的。*

***Cruise Automation 的**首席执行官描述了该公司与通用汽车仅用 14 个月就制造出的[三代](/kylevogt/how-we-built-the-first-real-self-driving-car-really-bd17b0dbda55)无人驾驶汽车！*

***Voyage** 希望在[封闭式退休社区](https://www.nytimes.com/2017/10/04/technology/driverless-cars-testing.html)测试他们的车辆——这篇文章强调了在加州每辆车需要 500 万美元的保险。Voyage 必须支付两倍的费用，并将传感器数据提供给保险提供商，这将有助于保险公司更好地为风险定价。*

## ***💪巨人队***

*本周，谷歌发布了[的 Pixel Buds](https://www.theverge.com/circuitbreaker/2017/10/4/16401132/google-pixel-buds-wireless-headphones-announced-price-release-date) ，贬低了**苹果**的 airpods。这些无线耳机让用户可以访问谷歌翻译，进行 40 种语言的实时翻译！谷歌还在宣布 Gradient Ventures 之后推出了一个 [Launchpad AI Studio](https://developers.google.com/startups/studio/) ，该工作室为使用机器学习[的初创公司提供数据集、模拟环境和来自谷歌员工的咨询帮助](https://hackernoon.com/tagged/learning)。该公司还发布了 [Google Clips](https://www.theverge.com/2017/10/4/16405200/google-clips-camera-ai-photos-video-hands-on-wi-fi-direct) ，这是一款面向父母(宠物)的可穿戴相机设备！)想要记录他们日常环境的图像和短片。这看起来像是叙事片段的更高级版本(从计算机视觉的角度来看)，该版本于 2012 年推出，但在获得 1200 万美元的风险融资后，[于 2016 年关闭了](https://techcrunch.com/2016/09/28/narrative/)。谷歌的 **DeepMind** 将其用于生成原始音频波形的最先进的 WaveNet 模型推送到谷歌助手中，以取代传统的文本到语音转换！最初发布的模型计算量太大，因为它以每秒 16k 样本的速度一次创建一个波形样本。尚未公布的新型号速度快 1000 倍，创建一秒钟的语音只需 50 毫秒。*

***Box** 正在运行 [beta 测试](http://fortune.com/2017/08/17/box-google-machine-learning/)与**谷歌的**云视觉 API，允许他们的客户运行图片搜索查询。我觉得这很有趣，因为这是一个例子，说明大型科技公司不会因为将数据和收入交给谷歌而感到威胁，因为这可能被视为核心竞争力。*

***亚马逊 AWS** 宣布[两项与机器学习世界相关的主要服务](https://aws.amazon.com/blogs/aws/aws-summit-new-york-summary-of-announcements/)。第一个是 Macie，这是一种新的安全服务，使用机器学习来帮助识别和保护存储在 AWS 中的敏感数据，防止违规、数据泄露和未经授权的访问，亚马逊 S3 是最初的数据存储。第二个是 Glue，它是一个无服务器的服务，可以抓取你的数据，推断模式，并用 Python 生成 ETL 脚本。另外，亚马逊的 Lab126 展示了他们可以使用 [GANs 生成与特定目标风格一致的新颖时尚商品](https://www.technologyreview.com/s/608668/amazon-has-developed-an-ai-fashion-designer/)。这种方法可以为他们未来的时装设计师提供灵感！*

*创建自己的数据当然是人工智能的口头禅。据报道，苹果正在[收集](https://www.menshealth.com/content/inside-apples-secret-performance-lab)“关于活动和锻炼的数据比历史上任何其他人类表现研究都多，”苹果健康技术总监杰伊·伯拉尼克说。“在过去的五年里，我们记录了 33，000 个会话，超过 66，000 个小时的数据，涉及超过 10，000 名不同的参与者。”该数据集用于改善和扩展他们手表的健康功能。颗粒实时健康监控的未来即将到来…*

## ***🍪硬件***

***微软**为他们的 Hololens 2 推出了一款[新的以人工智能为重点的协处理器](https://www.microsoft.com/en-us/research/blog/second-version-hololens-hpu-will-incorporate-ai-coprocessor-implementing-dnns/?tduid=(3455a421068fa7a824d1181630bdf09e)(266696)(1503186)(66960X1514734X8272276b5b996ef17cec82ff38204c2a)())，它将补充现有的全息处理单元在处理定制的飞行时间深度传感器、头部跟踪相机、惯性测量单元(IMU)和红外相机方面的作用。协处理器是完全可编程的，并针对深度学习模型进行了优化。关于这个欣欣向荣的芯片市场的更广泛的讨论将在[这里](https://www.wired.com/story/the-rise-of-ai-is-forcing-google-and-microsoft-to-become-chipmakers/)报道。微软还发布了几个新的软件包，致力于让模型更快地被训练和投入生产。这进一步蚕食了私人 ML 基础设施软件公司的机会。*

***方舟研究** [发表了关于机器人单位成本下降及其需求价格弹性的研究](https://ark-invest.com/research/industrial-robot-costs)，暗示我们(终于？)正处于加速采用的风口浪尖。一个很难系统地衡量，但对采用来说至关重要的因素是每个任务的容错能力。*

## *🎮**游戏和虚拟世界***

***DeepMind** 和**暴雪**发布[星际争霸 2 学习环境](https://deepmind.com/blog/deepmind-and-blizzard-open-starcraft-ii-ai-research-environment/) (SC2LE)，加速专注于强化学习和多智能体系统的 AI 研究。它包括一个暴雪 ML API 来连接游戏(环境，状态，动作，轨迹)，多达 50 万个匿名游戏回放，一个基于 Python 的 RL 环境和几个简单的基于 RL 的迷你游戏来进行性能基准测试。这个游戏特别有趣，因为它需要长期的计划和多主体的合作，并具有潜在的不同子目标。*

*OpenAI 发布了一篇博客文章，提供了他们为玩流行的电子竞技游戏 Dota 2 并击败世界顶级职业选手而构建的代理的高级描述。传统的 5v5 游戏模式要求两队各 5 名选手进行 45 分钟的比赛，这需要高水平的策略、团队沟通和协调才能获胜。OpenAI 的机器人玩的是 1v1 版本，这使得游戏更注重短期规划和机械技能，正如这里的[所概述的](http://www.wildml.com/2017/08/hype-or-not-some-perspective-on-openais-dota-2-bot/)。然而，这是一个壮举。*

***占主导地位的游戏引擎公司 Unity** 已经进入机器学习业务。他们推出了 [*Unity 机器学习代理*](https://blogs.unity3d.com/2017/09/19/introducing-unity-machine-learning-agents/) ，可以使用 Unity 编辑器创建游戏和模拟。在这些环境中，可以通过简单易用的 Python API，使用强化学习、神经进化或其他机器学习方法来训练智能代理。令人兴奋的项目，但与拥有 50 人的 [**PROWLER.io**](http://www.prowler.io/) 相比，在 Unity 员工方面似乎资源相对不足！*

*事实上，像这样的环境将是通过模拟自我游戏学习紧急行为的智能体发展的核心。继 **DeepMind** 的早期工作之后， [**OpenAI** 显示](https://blog.openai.com/competitive-self-play/)负责学习在相扑比赛中获胜的人形智能体会自动发现诸如铲球、闪避和假装之类的动作。*

## ***🏥医疗保健***

***Veritas Genetics** ，一家直接为消费者提供全基因组测序和产前检测及乳腺癌靶向筛查的公司，通过[收购生物信息学公司 **Curoverse**](https://www.wired.com/story/veritas-genomics-scoops-up-an-ai-company-to-sort-out-its-dna/) 进军人工智能领域。他们一起致力于改善疾病风险评分和因果关系。*

*在 IBM 沃森将解决癌症诊断和护理中的许多问题的高调承诺之后，[的房子倒塌了。](https://www.statnews.com/2017/09/05/watson-ibm-cancer/)一项统计调查显示,“它仍在为了解不同形式的癌症这一基本步骤而奋斗。只有几十家医院采用了该系统，这距离 IBM 在价值数十亿美元的市场建立主导地位的目标还有很长的路要走。在外国医院，医生抱怨其建议偏向美国患者和护理方法。*

*一项从 ct 图像预测肺癌的[开源竞赛](https://concepttoclinic.drivendata.org/)已经启动。它提供了对两个大型肺部 CT 数据集的访问。欧洲创业公司 **Aidence** ，目前处于[第三名](https://concepttoclinic.drivendata.org/algorithms)！*

*很明显，药物发现和开发是一项昂贵、漫长且低效的努力。初创公司纷纷涌入这个问题，承诺人工智能技术可以探索更大的化合物搜索空间，跟踪副作用的文献，并在计算机上验证化学相互作用。然而，[现实并不乐观](http://rbharath.github.io/liquidity-in-drug-discovery/):制药公司不会支付软件许可费，没有人体试验数据的候选人没有多大价值。垂直整合不鼓励协作*

## ***🆕人工智能的新举措和前沿***

*[**OpenMined**](http://www.openmined.org/) ，一个专注于在分散的私人数据上使用加密模型的联合机器学习的开源项目，正在全球范围内获得[社区的关注](https://openmined.github.io/Team-Map/)。现在有超过 1k 的 Slack 用户和 68 个活跃的 GitHub 贡献者！观看安德鲁·特拉斯克对这个项目的介绍。这个项目的关键见解是在保护用户隐私的同时，为分布式机器学习训练访问私人用户数据。*

*[**中国**](https://www.cfr.org/blog/beijings-ai-strategy-old-school-central-planning-futuristic-twist) 和 [**俄罗斯**](https://www.theverge.com/2017/9/4/16251226/russia-ai-putin-rule-the-world) 都做出了大胆的国家承诺，让 AI 成为他们未来的核心贡献者，或者说是统治世界的秘方(据后者称)。中国描述了一个中央规划的愿景，即到 2025 年，国家人工智能产业的价值将达到 600 亿美元(高于目前的 15 亿美元)。根据高盛的一份报告，中国的三步走计划如下:*

*   *到 2020 年，中国计划在人工智能方面赶上其他全球领导者；*
*   *到 2025 年，他们的目标是实现重大研究突破，并让人工智能推动行业改革；*
*   *到 2030 年，在基础人工智能研究和现实世界应用方面成为世界领先的强国。中国希望人工智能推动智能经济和社会，使中国成为全球强国。*

*中国“新一代人工智能发展计划”的一个[翻译版本](https://www.newamerica.org/cybersecurity-initiative/blog/chinas-plan-lead-ai-purpose-prospects-and-problems/)认为，它缺乏对实施战略的洞察力，并且提炼出更多的“向问题扔钱”的方法。无论如何，阿里巴巴、腾讯、百度、小米和吉利汽车(沃尔沃的所有者)都有很好的盈利条件。*

*随着**脸书**继 **TwentyBN** 和 **DeepMind** 在[发布了两个描述现实世界中场景、物体、动作和一般运动模式的标记数据集](https://venturebeat.com/2017/10/12/facebook-launches-two-datasets-to-improve-ai-video-analysis/)，视频理解空间正在升温*

***Keras** 作者 Francois Chollet 分享了[对机器学习的长期愿景](https://blog.keras.io/the-future-of-deep-learning.html)。最有趣的部分之一涉及到使用算法模块(形式推理、搜索、抽象)和几何模块(非形式直觉、模式识别)的原语为特定问题自动生成模型的想法。引用 Francois 的话:“给定一个新任务，一个新情况，系统将能够使用很少的数据组装一个适合该任务的新工作模型，这要归功于 1)丰富的类似程序的原语，这些原语概括得很好，以及 2)对类似任务的丰富经验。同样，人类可以用很少的游戏时间学习玩复杂的新视频游戏，因为他们有许多以前的游戏经验，并且因为从这种以前的经验中导出的模型是抽象的和类似程序的，而不是刺激和动作之间的基本映射。”*

*在题为 [***深度学习的信息理论***](https://www.youtube.com/watch?v=bLqJHjXihK8&feature=youtu.be) 的演讲中，希伯来大学的 Naftali Tishby 提出了一个框架和实验证据，证明深度学习模型(特别是 CNN)是如何如此有效地工作的。理论 [*信息瓶颈*](https://www.wired.com/story/new-theory-deep-learning/) ，本质上是假设深度学习模型尽可能压缩有噪声的输入数据，同时保留关于数据代表什么的信息。这意味着网络经历几个学习阶段:1)通过随机初始化的权重网络传递数据，2)通过使用随机梯度下降反向传播误差信号来调整权重，以及 3)丢弃关于输入数据的信息，仅跟踪最强的预测特征。然而，这个框架并没有解释为什么随着时间的推移，孩子们需要一个对象的珍贵的几个例子来将它的表示写入他们的记忆。在这里，很可能是学习到的原语的组合被组装起来以实现一般化。*

*Ian Goodfellow 被认为在 2014 年点燃了对**生成对抗模型**的研究，他阐述了[机器学习未来发展的几个领域](https://www.quora.com/What-is-next-for-deep-learning/answer/Ian-Goodfellow)。这些包括自动网络设计和优化——这是谷歌目前正在大力研究的领域。*

*2017 年 7 月发布了来自 **ImageNet** 的结果，38 个团队中的 29 个团队实现了低于 5%的错误率。现在，超过 1300 万张图像和网络达到了渐近性能，下一步是什么？[石英](https://qz.com/1034972/the-data-that-changed-the-direction-of-ai-research-and-possibly-the-world/)上的一篇文章和[费的演讲](http://image-net.org/challenges/talks_2017/imagenet_ilsvrc2017_v1.0.pdf)回顾 ImageNet 上的好/坏方法和未来的下一步都指出我们需要超越物体识别，进入人类对图像和视频的理解。*

*Saildrone ，但是为了天空！微软研究院正在研究一种自动滑翔机，这种滑翔机通过导航来寻找和利用温暖的空气，使它能够在不消耗能量的情况下停留在空中，就像一只鸟。*

*Matternet 也在向天空进军；这次在瑞士，它将[运行一个授权的无人机网络](https://www.theverge.com/2017/9/20/16325084/matternet-autonomous-drone-network-switzerland)在诊所之间运送医学实验室样本。英国的大麦农场也正在被无人机看管！*

*一项为期 10 年、耗资 2 . 4 亿美元的学术和工业研究合作创建了 [**麻省理工学院-IBM 沃森人工智能实验室**](http://mitibmwatsonailab.mit.edu/) ，其目标是推进四大研究支柱:人工智能算法、人工智能物理学、人工智能在工业中的应用，以及通过人工智能促进共享繁荣。*

***CIFAR** 解释了[元学习](https://www.cifar.ca/assets/machines-learn-new-ways-of-learning/)的领域，由此优化候选神经网络的学习算法不再是硬编码的(使用梯度下降),而是也转换为神经网络。这有助于没有数学模型来描述手头的问题。*

## *📖**政策***

*为全球公司提供人力资源指导的国际律师协会全球就业研究所[发布了一份关于人工智能对法律、经济和商业问题的影响的报告](https://drive.google.com/drive/folders/0Bxx383wVJ39Pb1p1eGhERTBGVDQ)，如未来劳动力市场和公司结构的变化，对工作时间、薪酬和工作环境的影响，新的就业形式以及对劳资关系的影响。*

*一份关于英国[**AI**](https://www.gov.uk/government/publications/growing-the-artificial-intelligence-industry-in-the-uk/recommendations-of-the-review)的重要独立报告已经发表，为政府出谋划策。该报告建议通过已建立的数据信托促进数据共享，为数据创建和共享提供公共资金，为洗钱创建 300 个新的硕士学位和 200 个博士学位(到 2025 年增加到 1，600 个博士学位)，以及其他举措。支持人工智能研究和商业化对英国科技行业来说是一个巨大的机遇，尤其是在英国退出欧盟遭受打击之后。我希望政府能帮助这个领域找到真正的动力。*

## *🔬**将人工智能放入上下文中***

*《大西洋月刊》刊登了一篇关于早期人类对抗机器世界锦标赛的报道；不是象棋或者围棋，而是 1994 年[的**跳棋**。这个故事让世界冠军选手、数学教授马里恩·汀斯利与一个程序 Chinook 进行了较量，Chinook 是阿尔伯塔大学的乔纳森·谢弗花了几千个小时辛苦编写并运行的。然而，在他们的现场比赛中，廷斯利因为健康原因不得不退出，几个月后他不幸去世。决心证明奇努克会赢，谢弗的唯一选择是完全解决游戏。他用最先进的试探法(AI)来证明双方完美的比赛会导致平局。题目恰如其分的科学 pas](https://www.theatlantic.com/technology/archive/2017/07/marion-tinsley-checkers/534111/)[*跳棋解*](http://science.sciencemag.org/content/317/5844/1518) 。*

*如今，对人工智能的狂热可能只受到加密令牌的挑战。一篇名为《人工智能预言的七宗罪》[](https://www.technologyreview.com/s/609048/the-seven-deadly-sins-of-ai-predictions/)*的文章，反思机器人学和 iRobot 创始人罗德尼·布鲁克斯提出了为什么我们必须特别小心让大胆的预言自由运行的理由。“许多人工智能研究人员和专家认为，世界已经是数字化的，简单地引入新的人工智能系统将立即渗透到该领域、供应链、工厂车间和产品设计的运营变化中。没有什么比这更偏离事实了。几乎所有机器人和人工智能领域的创新都需要比该领域内外的人们想象的更长、更长的时间才能真正广泛部署。”事实上，我们必须解决的另一个问题是传播和[放大训练数据中的偏差](https://www.technologyreview.com/s/608986/forget-killer-robotsbias-is-the-real-ai-danger/)。**

**祝贺您完成第 21 期第 1 部分，共 2 部分！有奖:)认识万能的[求职机器人](https://twitter.com/dataduce/status/897228678802755584)！**

**还有什么吸引你的吗？您对本新闻简报的内容/结构有何反馈？只需点击回复！**