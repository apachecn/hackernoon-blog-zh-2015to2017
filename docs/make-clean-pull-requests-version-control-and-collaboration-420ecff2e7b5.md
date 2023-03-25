# 提出干净的拉入请求—版本控制和协作

> 原文：<https://medium.com/hackernoon/make-clean-pull-requests-version-control-and-collaboration-420ecff2e7b5>

这是我的初级开发人员博客技术部分的第一部分。我来自一个非传统的编码背景，所以我努力工作，练习基础知识(总是编码)。我的博客有一半是关于技术主题的，另一半是关于平等、社会公正和技术进步的。

在过去的几周里，我开始了我的软件工程之旅。在众多要学的东西中，最具挑战性的一个是版本控制。您希望确保您的 [pull](https://hackernoon.com/tagged/pull) 请求是干净的，以便代码评审人员可以有效地梳理您的代码，并给您反馈，这将有助于提高其质量、性能和可读性。

想象一下这个场景，你完成了你的特征分支的工作。你感到自豪，并准备好用你令人敬畏的代码统治世界。您完成了正常的 [git](https://hackernoon.com/tagged/git) 流程，并意识到您已经将自时间开始以来与该特性相关的所有其他提交和文件更改带到了您的分支中。现在，您的 pull 请求中包含了无数个文件和数千个提交。

![](img/27b1a39d09fdeb14199a98fe08475764.png)

如果您是大型团队或大型代码库开发的新手，那么这种情况可能已经发生在您身上。在吃了几次这种令人沮丧的芯片后，我在家坐下来，浏览了 git 过程，以确保我每次都确切地知道如何创建干净的拉请求。

以下是创建您的分支副本和挑选提交的步骤，以确保只有那些文件和提交会被添加到您的 PR 中。

# 步骤:

1.  从你的上游剪下一根新的树枝
2.  将所有提交重组为一个。``git rebase -i HEAD~<number of commits from last>`或者`git rebase -i upstream/<repo-you-forked-from>`
3.  编辑你的提交，写一份描述性的提交，展示你的公关的整个故事

```
pick #Clear commit message that tells the full story of my feature
fixup #Commit Message 2
fixup #Commit Message 3 //fixup allows you to squash your commit and not include the message
```

4.检查您的日志，确保您只有一次提交``git log`

5.在您的上游执行硬重置，以确保您拥有最新的。

``git reset --hard upstream/<my-upstream>`

6.樱桃采摘你的承诺到你的新分支

``git cherry-pick <your-one-commit-number>`

7.修复任何合并冲突，并选择 HEAD 部分之后的最新更改

8.添加您的更改，提交并继续

```
git add -p
git commit -m "My Shiny New Branch With My Only Commits"
git cherry-pick --continue
```

9.将您的更改添加到新分支中

```
git push origin <new branch>:<old branch> -f
```

10.去你的 PR 链接，坐下来，享受你让你的开发者伙伴的生活变得更容易的事实

![](img/8d9e6a082445850e87eae44e0f225816.png)

Oh yeah!