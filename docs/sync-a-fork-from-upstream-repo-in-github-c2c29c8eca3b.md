# 从 GitHub 中的上游回购同步一个分叉！

> 原文：<https://medium.com/hackernoon/sync-a-fork-from-upstream-repo-in-github-c2c29c8eca3b>

> GIT 中的上游存储库是什么？

上游存储库是您从中派生存储库的父/原始存储库。

> 叉子是什么？

一个*分支*是一个存储库的副本。派生存储库允许您自由地试验变更，而不会影响原始项目。

我们将会看到如何在我们自己的分叉仓库中同步和合并上游仓库的变更？

当一个库被克隆时，它有一个名为 **Origin** 的默认遥控器，指向你在 GitHub 上的分叉，而不是它被分叉的原始库。
为了跟踪原始存储库，您需要添加另一个名为

> 如何配置上游存储库？

*   将目录更改为本地计算机上的 fork 存储库，并运行下面的命令( ***请在运行命令*** 时更改上游)

> *git 远程添加上游*[*https://github.com/abhishekamralkar/emacs.d*](https://github.com/purcell/emacs.d)

您可以通过以下命令来验证它

> git 远程-v
> 
> 下面应该是输出
> 
> 始发地[git@github.com](mailto:git@github.com):abhishekamrakar/emacs . d . git(fetch)
> 始发地[git@github.com](mailto:git@github.com):abhishekamrakar/emacs . d . git(push)
> 上游[https://github.com/aa/emacs.d](https://github.com/purcell/emacs.d)(fetch)
> 上游[https://github.com/aa/emacs.d](https://github.com/purcell/emacs.d)(push)

要将分叉存储库与上游存储库同步，请遵循以下步骤

1.  从上游存储库中获取分支及其各自的提交。

> *获取上游 git*

2.看看你的叉子在当地的主要分支。

> git 结账大师

3.将上游/主分支的更改合并到本地主分支。

> git 合并上游/主