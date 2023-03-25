# 不要使用 GitHub 的“重定基础和合并”工作流程(暂时)

> 原文：<https://medium.com/hackernoon/dont-use-github-s-rebase-and-merge-workflow-yet-4548f64aeb96>

去年 9 月， [GitHub](https://hackernoon.com/tagged/github) [为其“](https://github.com/blog/2243-rebase-and-merge-pull-requests) [Merge](https://hackernoon.com/tagged/merge) ”按钮引入了几个新的行为，作为每次创建新的合并提交标准的替代。即“挤压并合并”和“重设基础并合并”

如果你出于某种原因已经倾向于`git rebase`而不是`git merge`，那么“重建基础并合并”是很有吸引力的(在这里就不进行圣战了)。但是，它有一个缺陷，我按照以下步骤复制了这个缺陷:

1.  我创建了一个包含两个提交的 PR:[https://github . com/jcfrancisco/rebase-revert-bug-example/pull/2](https://github.com/jcfrancisco/rebase-revert-bug-example/pull/2)
2.  我用“Rebase”方法将 PR 合并到 master 中。
3.  我点击了那个 PR 上的 Revert 按钮，创建了这个 Revert PR:[https://github . com/jcfrancisco/rebase-Revert-bug-example/pull/3](https://github.com/jcfrancisco/rebase-revert-bug-example/pull/3)
4.  请注意，revert PR 仅撤销原始 PR 中两次提交中的第二次。

换句话说,“恢复”按钮实际上并不恢复 PR——它只是恢复该 PR 中的最终提交。对于那些依赖于回复按钮来立即撤销公关工作的产品或图书馆来说，这可能是一个交易破坏者。

所以先不要用“改基合并”。GitHub 正在解决这个问题——当我联系他们的支持团队时，他们说他们有一个“团队未解决的问题”,但“没有具体的修复时间表”他们还说修好后会发邮件给我，到时候我会更新这个帖子。

**更新:**看起来 GitHub 在我发邮件支持的几个小时内推出了一个修复程序，并且[恢复按钮现在可以正常工作了。](https://github.com/jcfrancisco/rebase-revert-bug-example/pull/5)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！