# 2017 年反思 F#

> 原文：<https://medium.com/hackernoon/reflecting-on-f-in-2017-5ac67fb138ff>

我真的很兴奋能参加今年的[fsevent](https://sergeytihon.com/tag/fsadvent/)。谢谢你，[谢尔盖](https://twitter.com/sergey_tihon)，谢谢你为这个和 [F#周刊](https://sergeytihon.com/tag/newsf-weekly/)所做的所有工作。

**三个免责声明:**

1.  我不是一个有创造力的人，特别是在给事物命名的时候，所以我为这篇文章偷了斯蒂芬·迪尔的标题。
2.  虽然我为微软工作，但我并不代表微软写这篇文章。这里表达的所有观点都是我自己的观点。
3.  这是一篇分成三部分的长文。我没有 TL；博士

2017 年对于 F#来说是非常激动人心的一年。

首先，F#已经变得比以往任何时候都大，至少就我们可以通过产品遥测、 [twitter](https://hackernoon.com/tagged/twitter) 活动、GitHub 活动和 F#软件基金会活动来衡量。

*   我们可以测量的 F#活跃用户数以万计。
*   今年，Ionide 为**的 Visual Studio 代码的独立用户增加了 50%** ，比以往任何时候都要多。
*   自去年以来，使用 F# i **的 [Visual Studio](https://www.visualstudio.com/downloads/) 的独立用户增加了 20%** 比以往任何时候都多，尽管今年早些时候的质量问题抑制了增长。
*   许多测量的增长与[的发布相一致。NET Core 2.0](http://dot.net/core) ，对 F#社区表现出了极大的兴趣。

遥测技术是一个复杂的话题，我们并不试图解释在没有遥测技术的环境中使用 F#的现有用户，所以它从来都不是完美的。F#在世界上的实际使用量远远高于我们所能测量的。

但是数字和指标是有限的，因为它们只讲述了故事的一小部分。我将尝试总结 F#今年发生的一些重要事情。

*   F# 4.1 发布了，这是该语言的第一个版本。NET 核心支持，并且自最初发布以来已经进行了多次更新。
*   [Visual Studio 2017 及其 F#工具](https://www.visualstudio.com/en-us/news/releasenotes/vs2017-relnotes#a-idfsharpnetcoreimprovements-a-f-tooling-improvements)出货**五大更新**，包括对[的支持。网芯和。NET 标准项目](https://blogs.msdn.microsoft.com/dotnet/2017/09/26/build-a-web-service-with-f-and-net-core-2-0/)。
*   F#社区中令人难以置信的成员，如[瓦西里·基里申科](https://twitter.com/kot_2010)、[索尔·伦尼森](https://twitter.com/saulren)、 [Anh-Dung Phan](https://twitter.com/dungpa) 、 [Steffen Forkmann](https://twitter.com/sforkmann) 和其他人对我们的 Visual Studio 2017 F#工具进行了重大改进，包括添加了令人眼花缭乱的功能阵列。
*   F#现在默认安装到 Visual Studio 2017 **中**如果你正在安装。网芯。
*   Visual Studio 2017 中的 F#工具现在有了一个[夜间发布通道](https://blogs.msdn.microsoft.com/dotnet/2017/03/14/announcing-nightly-releases-for-the-visual-f-tools/)。
*   [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/) 发布，[带有 F#支持的包装盒](https://docs.microsoft.com/dotnet/fsharp/get-started/get-started-with-visual-studio-for-mac)，它在这一年中不断改进。
*   Azure Functions 现在支持 F#，更好的功能即将推出。
*   [Azure 笔记本](https://blogs.msdn.microsoft.com/visualstudio/2016/12/05/azure-notebooks-now-support-f/)现在支持 F#。
*   FSharp。Core 现在是 F#核心库的官方包。
*   FSharp。Compiler.Tools 现在是部署 F#编译器 SDK 的官方网站，也是获得 SDK 的推荐方式
*   [F#现在与。网芯 SDK](https://blogs.msdn.microsoft.com/dotnet/2017/08/14/f-and-net-core-roadmap-update/) 。
*   我们扩大了我们的团队，并在年底雇佣了[威尔](https://twitter.com/TIHan)！
*   [F#语言建议](https://github.com/fsharp/fslang-suggestions)和 [F# RFC](https://github.com/fsharp/fslang-design) 库现在是 F#语言和工具发展的官方场所。
*   Ionide 是一个插件套件，它将 Visual Studio 代码转化为全功能的 F# IDE，至少发布了 100 个版本(或 200 个；呀，这么多)。
*   许多 F#项目中使用的令人敬畏的 NuGet 客户端 Paket ，今年似乎已经发布了数十亿个版本。
*   [JetBrains Rider](https://www.jetbrains.com/rider/) 配有优秀且不断改进的 [F#支架](https://github.com/JetBrains/fsharp-support)。
*   许多 F# OSS 生态系统已经迁移到[。网标](https://docs.microsoft.com/dotnet/standard/net-standard)和[。NET Core](https://docs.microsoft.com/dotnet/core/index) (这代表了维护人员难以置信的工作量)。
*   Suave 和 Giraffe 成为编写 web 服务时使用的两个主流库。NET 核心，芙蕾雅是一个很好的选择，每个人都应该尝试一下。
*   [寓言](http://fable.io/)从一个有潜力的有趣项目迅速演变成一个令人印象深刻的、成熟的浏览器 JavaScript 替代品，允许你编写 [**全栈 F#应用**](https://github.com/SAFE-Stack/SAFE-BookStore) 。
*   F#软件基金会(FSSF)[继续稳步增长](https://twitter.com/Thuris/status/936789720998166528)，并且成为最好的组织之一，如果你刚刚开始使用 F#的话。
*   FSSF 启动了它的[多元化项目](http://foundation.fsharp.org/announcing_the_diversity_program)，并迎来了又一轮免费的[导师项目](http://fsharp.org/mentorship/)。
*   OpenFSharp 被创造出来并销售一空(很快，尽管有低调的广告宣传)，这让我对美国的 F#充满了兴奋。
*   F#在 [//Build 2017](https://www.youtube.com/watch?v=8XY-rQx6Dug) 、 [NDC 会议](https://vimeo.com/223982162)上引人注目。 [NET](https://channel9.msdn.com/Events/dotnetConf/2017/T318) [Conf](https://channel9.msdn.com/Events/dotnetConf/2017/T319) 等功能性编程会议。

哦，那太多了。我可能错过了一些对人们很重要的事情，所以如果你觉得我应该列出一些事情，请让我知道。

如果有一件事是我在看上面的列表时感受最深的，那就是骄傲。不仅仅是我自己或我的同事，而是 F#社区的成员，他们在整个 F#生态系统的如此广泛的范围内做了如此多不可思议的事情。参与上述项目的每一个人都应该为自己和自己的成就感到骄傲。我是美国人，因此喜欢用最高级的词语，但你们都是摇滚明星，我很荣幸能和你们一起工作。

我还想提几件对我个人来说很重要的事情:

*   我很荣幸成为在 OpenFSharp 上宣布的[社区英雄](http://c4fsharp.net/#heroes)之一:

*   在 OpenFSharp 和 Rich Minerich 、 [Dmitry Morozov](https://twitter.com/mitekm) 、 [Gien Verschatse](https://twitter.com/selketjah) 、 [Mathias Brandewinder](https://twitter.com/brandewinder) 、 [Chet Husk](https://twitter.com/ChetHusk) 、 [Cezary Wojcik](https://twitter.com/cezary_wojcik) 、 [Marnee Dearman](https://twitter.com/MarneeDear) 和 [Marcus Griep](https://twitter.com/neoeinstein) 边喝啤酒边聊着各种疯狂的事情之后，我熬到了凌晨 3 点半。马蒂亚斯好心地提供了他的公寓作为这些恶作剧的场所。
*   我和[克日什托夫·谢拉克](https://twitter.com/k_cieslak)、[托马斯·皮特里切克](https://twitter.com/tomaspetricek)、[伊芙琳娜·加巴索娃](https://twitter.com/evelgab)、[马库斯·格里普](https://twitter.com/neoeinstein)、[里卡多·特雷尔](https://twitter.com/TRikace)、[吉恩·韦沙泽](https://twitter.com/selketjah)、[马克·格雷](https://twitter.com/MarkRGray)、[保罗·迈克尔·布拉苏奇](https://twitter.com/pblasucci)、[恩里科·萨达一起出去玩啤酒，食物，接管一个酒吧，在每个城市里嬉戏，享受阳光，](https://twitter.com/enricosada)[谈论 Golang](http://i.imgur.com/EVc3Nm0.png) ，以及其他发生的一切。太有趣了。
*   我亲眼目睹了粉红 Pezi 本人，Ross McKinlay，在 F#交易所偷了 T2 Eirik tsar palis 的徽章，然后自称是希腊程序员 Eirik。Ross，将来会有 EirikTsarpalisProvider 类型的提供者库让我使用吗？它是只提供名为“Ross”的类型，还是也提供名为“Eirik”的类型？
*   我是为数不多的被安德烈·马格诺斯基[以唐·赛姆的方式赶着离开 F#交易会场的幸运儿之一。](https://twitter.com/silverSpoon) [Hup hup！](http://www.pinksquirrellabs.com/blog/2016/08/23/don-syme-2-0-cow-herding-edition-type-provider/)
*   在 F# eXchange 上，我不得不让 Andrea Magnorsky 把我拉到一边，进行了一次精彩而严肃的谈话，讨论社区需要从我这里听到的各种事情。哦，她还给我的笔记本电脑贴了一张 FunctionalCat 贴纸，我至今仍自豪地展示着它！
*   2014 年，当我还是一名大学生的时候，我见到了斯科特·沃斯钦，他丰富的博客让我明白了为什么 F#如此伟大。
*   我看了阿方索·加西亚·卡罗(Alfonso Garcia Caro)推出代号为 Narumi 的寓言 1.0，很荣幸他在第九频道上出现了两次，而不是一次。
*   在伦敦的时候，我和 G-Research 的一些很棒的人一起玩了很多次。
*   我开始与[打交道，每日与一些](https://twitter.com/sforkmann) [惊人的](https://twitter.com/kot_2010) [OSS](https://twitter.com/cloudRoutine) [贡献者](https://twitter.com/enricosada)到 F#及其生态系统。
*   我必须让唐·赛姆认为我的许多建议都是好主意。对于一个 2015 年 6 月才获得大学学位的人来说，这是一件大事！

我认为在我的领域里很少能找到这么有回报的工作。压力很大，需要解决的问题从来都不简单。但每次都是值得的。

现在，为了一些完全不同的东西。

我不认为我曾经为 F#社区的任何人澄清过，我在微软的确切角色是什么。我目前的头衔是“项目经理 II”，但这并不意味着什么。我先列出我不是**的事情:**

*   软件工程师。我是项目经理！但是我有时会为产品写代码。
*   项目经理。不不不不不。我不擅长程序。
*   经理。没人为我工作。
*   程序。我是人，不是程序。
*   福音传道者。没有。虽然我有时会做一些传教的事情。
*   销售人员。你真的不想让我卖东西给任何人。
*   数据科学家。差远了。但是我有时使用查询语言在遥测系统的泥淖中摸索。
*   产品经理。这是最接近的，但和其他科技公司的其他产品经理做的不一样。我有一些我跟踪的 KPI，但是我把它们保持在较低的数量上，并且倾向于不太关注它们。实际上，我已经受够了我们这个行业所采用的那些愚蠢的、只注重边缘有用的关键绩效指标，更不用说这些指标是用不正确的方法得出和衡量的了。

我采取的每一个行动都是为了确保 F#是一个愉快的合作伙伴，并朝着正确的战略方向前进，这样它将在未来的岁月里发展壮大。这意味着几件事:

*   对于任何给定的与 F#有某种关联的产品或主题，我都有一些上下文。背景深度可能会有很大差异。
*   我帮助制定了很多战略和战术决策，关于微软如何利用它所拥有的资源为 F#做些什么。
*   我花了大量时间与微软的其他团队互动。有时，这些互动仅仅是给某人提供他们所要求的背景。其他时候，这是一个完全成熟的谈判过程，在这个过程中，我们就在某些情况下谁负责资产的交付和维护达成共识。
*   我负责一些和 F#没有直接关系的事情。目前，这包括 Visual Studio 2017 设置的某些部分以及我们组织围绕 Azure 增长所做的努力。这项工作最近的实际成果是，无论何时安装 Visual Studio，默认情况下都会安装 F#。网芯。
*   当我参与有关的讨论和决策时，我总是代表 F#开发人员提出建议。NET、Visual Studio 和 Azure。我对事情的默认立场是它们是否对 F#用户有意义。
*   我一直在学习更多关于我们的代码库的知识，并且我一直努力在高层次上理解可移动的部分，以便我可以向任何人描述它们。代码库庞大而密集，所以有些东西我完全不知道。我的希望是，到 2018 年底，我可以为那里的一些“更深入”的领域做出可靠的贡献。
*   当我有时间的时候，我会宣扬 F#，微软为 F#提供的工具，以及各种各样的 F#开源项目。我希望继续扩大这一努力。

最后，我最终是为了 F#的成功，使用 F#的 Visual Studio 用户，使用 F#的 Visual Studio for Mac 用户，使用 F#的 Visual Studio Code 和 Ionide 用户，以及使用 F#的 Azure 用户而上钩。虽然我刚刚列出的许多内容不是我的直接责任，但我在年底的审查取决于我对 Visual Studio 附带的 Visual F#工具之外的内容的参与。

这一点很关键:我的角色使 Visual Studio 中的 Visual F#工具黯然失色。F#不仅仅是微软在 Visual Studio 中提供的资产，将我的行为限制在这些方面会对 F#用户造成伤害。整个“F#体验”的整体图景是我所关心并试图改善的。

现在，为了一些完全不同的东西。

对于 2018 年将会为 F#带来什么，我感到非常兴奋。在 2017 年，F#生态系统中的许多事情都已经平静下来，并为成功做好了准备。以下是我对来年的一些想法:

*   我相信，F#将实现成为这个星球上工具最好的函数式编程语言的愿景，在你喜欢的任何 IDE 中都有出色的支持，在 Azure 中也有出色的支持，并且能够在整个 ALM 体验中使用你想要的工具。
*   我相信 F#社区，尤其是通过 F#软件基金会，将会继续成长，并成为语言新手的活跃家园。
*   我相信这个寓言会比今年在 JavaScript 社区获得更大的兴趣。
*   我相信服务器上的 F#通过。NET Core 将是许多人和组织的一个引人注目的选择。
*   我相信 F#会比 2017 有更强的大会存在感。
*   我相信 F# OSS 活动的增加。NET Core，而寓言将推动 F#在使用量和活跃度上达到新的高度。

我还将分享一些我个人希望关注的我的团队交付的东西:

*   正在将类型提供程序最终确定为。NET 标准 2.0 组件。
*   提供 F#交互式支持。NET Core，作为一个. NET 核心应用程序，可通过命令行上的`fsi`访问，通过. NET CLI 全局工具安装。
*   通过 F# Interactive 中的`#r “packagename"`进行包管理，运行于。网芯。
*   正在完成。Visual Studio 2017 中基于. NET Core SDK 的项目支持，包括多目标和文件排序，Azure 函数和 ASP.NET 核心的模板，以及合并社区的相关模板。
*   完成对 F#编译器服务的详细性能分析，并通过工程工作解决发现的问题。
*   继续 F# 4.1 的工作以获得更好的错误消息。
*   努力发布新的语言版本。
*   新的和改进的特性，比如能够定义元数据中定义的类型，或者改进的自动完成。

我认为以上是一系列相当现实的事情，你们在接下来的一年都会喜欢。我非常希望到明年圣诞节时，这些东西都能成为值得夸耀的东西。

2018 年对于 F#来说将是不可思议的一年。直到那时！