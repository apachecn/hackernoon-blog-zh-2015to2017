# 将 140，000 个提交从 Mercurial 迁移到 Git

> 原文：<https://medium.com/hackernoon/migrating-140-000-commits-from-mercurial-to-git-5cf46f134261>

最近，我自愿负责将我的团队从 Mercurial 迁移到 Git。公司的大部分人已经使用 Git 几个月了，这个特殊的库——我的团队只使用了其中的一小部分——是 Hg (Mercurial)中最大的库。

在过去的 6 年里，这个存储库被用作各种项目的单一报告。大约有 50 个 Visual Studio 解决方案引用了 200 多个项目，总历史记录接近 140，000 次提交。“嘟——ch。hg/`告诉我历史超过 1GB，还有` du -ch。/* `报告当前内容达到大约 800MB。我这次迁移的目标之一是提高存储库的性能，因为从头开始克隆存储库是一整天的事情，我尝试过的 Hg GUI 工具都在历史的重压下苦苦挣扎。幸运的是，在根级别的 180 个文件夹中，我的团队只关心 1 个。

那么，我怎样才能在不破坏所有历史的情况下移除我们不用的东西呢？

首先，我研究了有哪些工具可以在保存历史的同时将 Hg 存储库转换成 Git。GitHub 实际上提供了一个[存储库导入器](https://help.github.com/articles/about-github-importer/)，可以在创建新的 repo 时从 Hg 导入，还有一个名为[的开源项目 fast-export](https://github.com/frej/fast-export) ，可以在本地做同样的事情。后来在这个过程中，我还发现了宣传类似功能的[Mercurial ConvertExtension](https://www.mercurial-scm.org/wiki/ConvertExtension)。

我最初的努力并不令人鼓舞。转换 140k 的历史条目需要一段时间，所以直到大约一个半小时后，我才从 GitHub importer 那里得到消息，存储库太大，无法导入，又过了 45 分钟,“快速导出”失败，并出现一个关于提交日期无效的错误。Drat。使用'- force '再次尝试快速导出，通过了第一个错误，但在转换过程中的另一个无效日期失败。我后来知道这些错误不是由日期引起的，这只是一个症状。

鉴于这些错误发生在几个月前的修订版上，而且我无论如何都想减小存储库的大小，我开始寻找一种方法在转换到 Git 之前删除历史。我发现[Mercurial convert extension](https://www.mercurial-scm.org/wiki/ConvertExtension)据称可以进行 Hg > Hg 转换(以及更多),并提供从特定版本开始、从历史中过滤文件的选项，以及各种强大的选项。但是，它总是失败，并出现一个关于无效压缩类型的模糊错误，大约是历史记录的四分之三(` Abort: invalid compression type 'k '和` compression type '6 '都出现过。没什么帮助)。IRC、StackOverflow 和多年的邮件列表都无法帮助我让它工作，在一天没有进展之后，我不得不放弃这个看起来很有前途的工具。

经历了这些失败后，我和一位过去做过类似转变的同事聊了聊。他给我指了“快速导出”和两个用于大规模修改历史的工具，分别叫做 [git-filter-branch](https://git-scm.com/docs/git-filter-branch) 和 [BFG 回购清理器](https://rtyley.github.io/bfg-repo-cleaner/)。他还提到了我看到的同样的无效日期问题，并给我指出了一个解决方案——错误不是日期，而是一个空作者导致了列错位。用那个“authors.txt”文件运行“快速导出”成功了！大约一个小时后，我有了一个等效的 Git 存储库，并有了备份，这样我就可以随心所欲地修改历史，而不必从头开始重新导出。为了使它与我的同事所做的更改保持同步，只要导出的 Git repo 没有任何新内容，fast-export 就会进行增量导出。

现在我在 Git 中有了我们的完整历史，我可以开始尝试将其缩减到可管理的大小。

BFG Repo-Cleaner 的一些试运行表明，它非常擅长这项工作，在一两分钟内将` . git '文件夹的大小从导出后的大约 1.6GB 减少到大约 100MB。但它仍然留给我相同数量的提交，这意味着有成千上万的空修订堵塞了历史。“git-filter-branch”支持使用“- prune-empty”来修剪空提交，但是一些实验表明，完全处理历史记录需要 2 个多小时。

幸运的是，不仅有人[开了一个 PR](https://github.com/rtyley/bfg-repo-cleaner/pull/147) 给 BFG 回购清理器添加了“prune-empty-commits ”,而且当它在几个月后还没有合并时，他们[发布了一个. jar！](https://github.com/rtyley/bfg-repo-cleaner/pull/147#issuecomment-224770369)有了这个。jar 和新的标志，git 历史记录已经从大约 14 万次提交减少到大约 3 万次，而`. Git `文件夹减少到大约 70MB。仍然是一个大的回购，但一个巨大的，巨大的改进。为了彻底起见，我随后运行了“git filter-branch - prune-empty ”,看看是否有更多的提交需要查找。它将历史记录减少到大约 27k 次提交，花费了大约 25 分钟——这是一个小改进，需要花费相当长的时间，但对于一次性迁移来说还是值得的。

最终，我不得不编写两个脚本来自动化这个过程。一个脚本在我们需要保留的每个分支上运行，删除不必要的文件并创建一个`. gitignore `,另一个“协调”脚本执行整个 repo 范围的任务，如重命名和删除分支，在每个分支上运行另一个脚本，从历史记录中删除删除的文件，并对 git repo 进行垃圾收集。

概括地说，在迁移之前，我们有:

*   6 年的历史
*   1.2GB 英寸。汞
*   800MB 的当前文件
*   大约 50 个 Visual Studio 解决方案
*   大约 200 个 Visual Studio 项目
*   140，000 次提交
*   大约 180 个我的团队不使用的顶级文件夹

迁移之后，我们只剩下:

*   仍然有 6 年的历史
*   70MB 英寸。饭桶
*   15MB 的当前文件
*   0 个解决方案
*   0 个项目
*   27，000 次提交
*   没有我们不用的文件

成功使用的工具:

*   [快速导出](https://github.com/frej/fast-export)，以及[自定义 authors.txt](https://github.com/frej/fast-export/issues/15#issuecomment-65903469)
*   [BFG 回购清洁器](https://rtyley.github.io/bfg-repo-cleaner/)(以及一个未发布的[罐子](https://github.com/rtyley/bfg-repo-cleaner/pull/147#issuecomment-224770369)
*   [git-filter-branch](https://git-scm.com/docs/git-filter-branch)
*   一些定制的 bash 脚本将它们结合在一起。

我的经验教训:

改变 SCM 系统和大规模修改历史的过程实际上比我想象的要容易得多。我带着对 Hg 的负面印象离开；它允许将导致多个工具失败的损坏提交到历史中。操作 Git 历史的工具比 ConvertExtension 简单得多。我花了很多时间阅读文档，并在 IRC 上询问相关问题，但我从未感觉到自己离理解它应该如何配置更近了一步。这给了我另一个证明，不管你认为你的问题有多奇怪和独特，其他人已经做了同样的事情。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！