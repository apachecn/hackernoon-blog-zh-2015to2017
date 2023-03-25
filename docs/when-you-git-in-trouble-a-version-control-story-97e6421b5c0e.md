# 当你陷入困境:一个版本控制的故事

> 原文：<https://medium.com/hackernoon/when-you-git-in-trouble-a-version-control-story-97e6421b5c0e>

谢谢你笑我这个极其搞笑的标题。但你知道什么不好笑吗？当您对 git repo 进行提交时，您会在 [GitHub Desktop](https://desktop.github.com/) 中看到:

![](img/726a7e12752db14b82cc680575a895ed.png)

Try using Google to figure out what this means

是的，我知道[酷人](http://www.worldcat.org/oclc/982603508)使用 [Git 塔](https://www.git-tower.com)并且*真正的*酷人只是使用[命令行](http://www.cryptonomicon.com/beginning.html)。我们真的是很酷的人，所以我们要用命令行来解决这个问题。事实上，我们别无选择——这就是你在本文中加入我的冒险:修复一个 git repo，它突然被损坏，这完全不是你自己的错，尽管没有任何命令行 git 专业知识。但至少你看到了我的恐慌。

第一步是诊断问题。然而，如果你像我一样，你也有一个[元问题](http://wiki.c2.com/?MetaProblem)，这来自于依赖一个你几乎不了解的工具，这使得[诊断](https://hackernoon.com/tagged/diagnosis)变得困难。找到有同情心的专家来帮助你解决你的具体问题也是一个挑战。你可能甚至不知道该问谁或者应该问什么问题。然而，你可以学习事情是如何运作的，如何一点一点地解决你自己的困境——如果你有耐心，有系统，并且愿意学习的话。朋友们，这让我不得不去了一趟 [git 参考文档](https://git-scm.com/docs)，在那里我发现了 [git-fsck](https://git-scm.com/docs/git-fsck) 命令，我尽职尽责地在我的 repo 的根目录中运行了该命令，并产生了以下(截断的)输出:

```
> git fsck

...

error: object file .git/objects/67/99ddac675cab54060cdfb066dbfadb6708fc3f is empty
error: object file .git/objects/67/99ddac675cab54060cdfb066dbfadb6708fc3f is empty
fatal: loose object 6799ddac675cab54060cdfb066dbfadb6708fc3f (stored in .git/objects/67/99ddac675cab54060cdfb066dbfadb6708fc3f) is corrupt
```

因此，如果你的一个项目出现了 [blinkies](http://pacman.wikia.com/wiki/Blinky) 的情况，你可能会发现这里有一个*损坏的存储库*。哦。很高兴知道。现在怎么办？当你想知道生命的意义时，你问[神](https://www.bobdylan.com/)。在这种情况下，我查阅了来自 Linus Torvalds 的一封[古老的电子邮件，它恰好解决了类似的情况。](https://git.kernel.org/pub/scm/git/git.git/tree/Documentation/howto/recover-corrupted-blob-object.txt)

git repo 实际上是各种[二进制对象](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)的[图](https://en.wikipedia.org/wiki/Graph_(abstract_data_type)):blob、trees 和 commits。Blob 对象是[加密散列的](https://en.wikipedia.org/wiki/SHA-1)，嗯，blob 是你的数据，每个 blob 代表你的一个文件。这些 blob 对象彼此独立，但它们也通过所谓的树对象链接在一起，树对象有效地将 blob 分组到类似于文件系统目录结构的排列中。最后，还有 commit 对象，它包含跟踪树和 blobs 中的变化所必需的信息。提交对象也是顺序链接的(如您所料)。

Git 根据提交 id 的前几个字符将所有这些对象存储在一系列嵌套目录中，这些目录位于`.git/objects/`中(如上所示)。比如对象`6799ddac675cab54060cdfb066dbfadb6708fc3f`作为文件`99ddac675cab54060cdfb066dbfadb6708fc3f`存放在一个名为`67/`的目录下；也就是说，完整的对象名是其存储目录和该目录中特定文件的组合。

因此，如果其中一个提交对象被破坏，您的整个回购可能会变成一堆无用的字节，因为链接的提交链将会被破坏。这是坏消息。好消息是，正因为你的回购是一个离散文件的集合，即使其中一个对象损坏得无法修复，你也可以恢复它的健康——如果你能进行足够精确的手术。

这就是我想做的。

按照 Linus 的建议，我将损坏的提交对象文件`./git/objects/67/99ddac675cab54060cdfb066dbfadb6708fc3f`移到了其他地方。您可以将损坏的物品停放在您喜欢的任何地方。不管怎样，它们最终可能会被扔进垃圾桶。

我碰巧得到了 blob 对象`67a45ac2f58a444fa4db11cd9ab7e024a8e35dcf`的相同错误消息，所以我也移动了那个对象。然后，我再次尝试检查文件系统:

```
> git fsck

Checking object directories: 100% (256/256), done.
Checking objects: 100% (8970/8970), done.
broken link from    tree 03a88f876eb3f6157f76461a3ae6cb18bbb86561
              to    blob 67a45ac2f58a444fa4db11cd9ab7e024a8e35dcf
dangling commit 76814e15074b540bc2f7e78daf3f5175a8759523
missing commit 6799ddac675cab54060cdfb066dbfadb6708fc3f
missing blob 67a45ac2f58a444fa4db11cd9ab7e024a8e35dcf
dangling blob 2a60520000698ad964e4e61fab31f9b862763550
dangling commit 41634cd81964068acb153bfa355d63bd80fc7cef
dangling commit 5bf415e2bdbc47822ae99b64c2a0f6b4f288eefb
```

注意，Linus 建议使用`git fsck --full`，但这是现在的默认行为。

忽略“悬空提交”消息,“断开链接”消息告诉我哪个树对象指向我刚刚删除的 blob 对象。实际上，我是故意断开链接来透露这个信息的。树对象`03a88f876eb3f6157f76461a3ae6cb18bbb86561`期望指向斑点`67a45ac2f58a444fa4db11cd9ab7e024a8e35dcf`，但是斑点不在那里。我移动的另一个提交对象`6799ddac675cab54060cdfb066dbfadb6708fc3f`也被报告丢失。到目前为止，一切顺利。

继续 Linus 的建议，我现在有足够的信息来使用 [git-ls-tree](https://git-scm.com/docs/git-ls-tree) 命令列出上面调用的树对象的内容:

```
> git ls-tree 03a88f876eb3f6157f76461a3ae6cb18bbb86561

100644 blob 312d8994f1005a9563a9410c592b27000c201101	building-test.js
100644 blob f84006fd14c6d4b2ccc3ef22b2fe02abf535bd1a	folds-test.js
100644 blob 67a45ac2f58a444fa4db11cd9ab7e024a8e35dcf	index.js
100644 blob 8b1f47bce7ec989dff7e936279d63d1d02f6a92d	indexing-test.js
100644 blob 3f2b45f8cd9dfd486c8e821ee672ed66a34768df	inf-test.js
100644 blob 0e6d1985aa59d17e2115bc6c7936d2ac88b00457	list-test.js
100644 blob 40310b5df53691d0e1ba4118c0e3ab66ed766990	reducing-test.js
100644 blob 8244d5fb2768ad5c7c33890ee26c797c2df6262b	searching-test.js
100644 blob ff34a2f15faf6eec7d9c9635e79d2a0abdadfb42	sub-test.js
100644 blob ac0c029cc64870c7445c4bdd9d7fe20646b5cc33	trans-test.js
100644 blob 99781d303e90b7aa4de8d630c1053a42f87e8331	zip-test.js
```

浏览列表，我找到了罪魁祸首 blob `67a45ac2f58a444fa4db11cd9ab7e024a8e35dcf`及其相关文件:`index.js`。所以现在我知道了问题的来源，但是我不知道那个文件的哪个版本首先产生了问题。回到命令行:

```
> git log --raw --all

commit cf63a71497e027d96614cfff6ba1d297f1a1a26e
Author: Steven Syrek <steven.syrek@example.com>
Date:   Mon Jul 18 11:55:40 2016 -0400

    Add tests for set operations on lists

:100644 100644 67a45ac... c1c2f99... M  test/list/index.js
:000000 100644 0000000... 23c47fe... A  test/list/set-test.js

commit f3bc2c55b22deb889f99cdd45663c20a8e8e79c1
Author: Steven Syrek <steven.syrek@example.com>
Date:   Mon Jul 18 11:14:13 2016 -0400

    Add tests for list zipping and unzipping functions and remove exponentiation operator from tests and examples

:100644 100644 01af47b... 3b1bf35... M  source/list/zip.js
:100644 100644 21206e2... 67a45ac... M  test/list/index.js
:000000 100644 0000000... 99781d3... A  test/list/zip-test.js
```

带有`--raw`和`--all`选项的 [git-log](https://git-scm.com/docs/git-log) 命令将显示一个回购的整个提交历史。我只展示了我上面的相关部分。我们在这里可以看到，对象`21206e20386e0365bc6f15d0ccd372b1c72b5667`在损坏的对象`67a45ac2f58a444fa4db11cd9ab7e024a8e35dcf`之前，而在后续的提交中(它们以相反的顺序列出)对象`c1c2f99072ef41aca89e963cfb0143f897e0de78`紧随其后。

此时，Linus 说我完成了，因为我发现了在损坏的提交之前和之后的文件版本:

> 如果你能做到这一点，你现在可以用`git hash-object -w <recreated-file>`重新创建丢失的对象，你的存储库就又好了！

不幸的是，在我尝试了这个之后，我的存储库又不好了。现在事情[开始变得棘手](http://www.imdb.com/title/tt0090142/)。过去的莱纳斯不在建议之列，现在的莱纳斯(现在也是过去的莱纳斯)可能有比帮我更好的事情要做。因此，我不得不遵循[专业开发人员](http://timemachine.wikia.com/wiki/Morlocks)每天使用的精明的故障排除流程:

谷歌。谷歌。

堆栈溢出。omg 它倒下了

这种孤注一掷的方法让我尝试了一些东西，从 [git-diff](https://git-scm.com/docs/git-diff) 命令开始。如果我不能自动重建丢失的物体，我绝望地想，也许我可以手动重建:

```
> git diff 206e20386e0365bc6f15d0ccd372b1c72b5667..c2f99072ef41aca89e963cfb0143f897e0de78

fatal: ambiguous argument '206e20386e0365bc6f15d0ccd372b1c72b5667..c2f99072ef41aca89e963cfb0143f897e0de78': unknown revision or path not in the working tree.
```

哎呀。我忘了主角:

```
> git diff 21206e20386e0365bc6f15d0ccd372b1c72b5667..c1c2f99072ef41aca89e963cfb0143f897e0de78

diff --git a/21206e20386e0365bc6f15d0ccd372b1c72b5667..c1c2f99072ef41aca89e963cfb0143f897e0de78 b/c1c2f99072ef41aca89e963cfb0143f897e0de78
index 21206e2..c1c2f99 100644
--- a/21206e20386e0365bc6f15d0ccd372b1c72b5667..c1c2f99072ef41aca89e963cfb0143f897e0de78
+++ b/c1c2f99072ef41aca89e963cfb0143f897e0de78
@@ -25,3 +25,7 @@ export * from './sub-test';
 export * from './searching-test';

 export * from './indexing-test';
+
+export * from './zip-test';
+
+export * from './set-test';
```

上面是`index.js`中在被破坏的提交的任一侧的两次提交之间发生变化的行。它们标有一个`+`，周围还显示了几行用于上下文。被删除的行，如果有的话，会被标上一个`-`。由于两个相同的文件，当[散列](https://git-scm.com/docs/git-hash-object)时，应该产生相同的散列键，我想我会尝试通过删除更改的行并手动重新创建提交来强行解决:

```
> git hash-object -w ./test/list/index.js

2a60520000698ad964e4e61fab31f9b862763550
```

没有。再试一次，也许只是删除标记为`+`的行。

```
> git hash-object -w ./test/list/index.js

21206e20386e0365bc6f15d0ccd372b1c72b5667
```

不，但很有趣。在损坏的提交发生之前，我设法重新创建了对象的原始状态，但我想我真正想做的是重新创建正确的中间状态？幸运的是，没有太多的可能性，因为我一反常态地经历了一个[良好的饮食卫生](http://www.ericbmerritt.com/2011/09/21/commit-hygiene-and-git.html)阶段。所以我再次修改了文件，只添加了那些标有`+`的行，我记得是在所有东西都坏掉之前添加的:

```
git hash-object -w ./test/list/index.js

67a45ac2f58a444fa4db11cd9ab7e024a8e35dcf
```

耶。

```
> git fsck

Checking object directories: 100% (256/256), done.
Checking objects: 100% (8970/8970), done.
dangling commit 76814e15074b540bc2f7e78daf3f5175a8759523
missing commit 6799ddac675cab54060cdfb066dbfadb6708fc3f
dangling blob 2a60520000698ad964e4e61fab31f9b862763550
dangling commit 41634cd81964068acb153bfa355d63bd80fc7cef
dangling commit 5bf415e2bdbc47822ae99b64c2a0f6b4f288eefb
```

哦，对了，我现在有一个健康的 blob，但是我仍然缺少指向它的提交对象。现在怎么办？是 [git-gc](https://git-scm.com/docs/git-gc) 来救援了！

```
> git gc

error: Could not read 6799ddac675cab54060cdfb066dbfadb6708fc3f
error: Could not read 6799ddac675cab54060cdfb066dbfadb6708fc3f
warning: reflog of 'HEAD' references pruned commits
warning: reflog of 'refs/heads/restructure' references pruned commits
error: Could not read 6799ddac675cab54060cdfb066dbfadb6708fc3f
fatal: Failed to traverse parents of commit b267a6a8264c0cdc72d047049610fc91e9f7c06f
error: failed to run repack
```

或者没有。那应该是[垃圾收集](https://www.youtube.com/watch?v=PEjaxYmxY8E)所有的……垃圾。修复…所有的东西。我不知道我为什么会这样想。但我希望。我真的真的希望。然后我[祈求](https://www.youtube.com/watch?v=nrG-uoVJHwk)。注意到上面的“参考日志”信息，我想到了下一个绝妙的主意:

```
> git reflog expire --all --stale-fix

error: Could not read 6799ddac675cab54060cdfb066dbfadb6708fc3f
fatal: Failed to traverse parents of commit b267a6a8264c0cdc72d047049610fc91e9f7c06f
```

众所周知，当使用[命令行工具](https://git-scm.com/docs/git-reflog)时，你添加的选项越多，你就越有男子气概。*如果你不知道他们做什么也没关系*。真正的男人不会阅读`man`页:他们只是快速移动[并打破事物](https://www.nytimes.com/news-event/russian-election-hacking)。此外，我很喜欢重新拍卖我的回购的想法。但没有。这也没用。

到目前为止[已经完全投入到](https://www.opensourceshakespeare.org/views/plays/play_view.php?WorkID=macbeth&Act=3&Scene=4&Scope=scene&LineHighlight=1434#1434)的尝试中，完全不顾[的感觉](https://www.goodreads.com/book/show/6425725-sense-and-sensibility-and-sea-monsters)或[的健全](https://en.wikipedia.org/wiki/Depth_sounding)。我回头看了看那些圆木，它们总是觉得离承认失败还有一步之遥，找个角落静静地哭泣。但是，也许一个解决方案会奇迹般地出现，这是我之前错过的，但是所有人都可以看到吗？

```
> git log 6799ddac675cab54060cdfb066dbfadb6708fc3f

fatal: bad object 6799ddac675cab54060cdfb066dbfadb6708fc3f
```

没有。

```
> git ls-tree 6799ddac675cab54060cdfb066dbfadb6708fc3f

fatal: not a tree object
```

没有。我是说，呃。不知何故，我有了一个好主意，只为我的回购的`restructure`分支检查日志，这是当致命的闪烁光标进入我的生活时，我一直在做的一个分支:

```
> tail -n 40 .git/logs/refs/heads/restructure

...

44dc22e706fb029a9c96f3bd125755fd55ac882b 6799ddac675cab54060cdfb066dbfadb6708fc3f Steven Syrek <steven.syrek@example.com> 1468788351 -0400	commit: Replace isEq function in all tests with should.eql
6799ddac675cab54060cdfb066dbfadb6708fc3f b267a6a8264c0cdc72d047049610fc91e9f7c06f Steven Syrek <steven.syrek@example.com> 1468789759 -0400	commit: Separate out functions in Ord tests

...
```

嗯。“也许我对 blob 对象所做的相同的不同的事情将对提交对象起作用，”我想。所以:

```
> git diff b267a6a8264c0cdc72d047049610fc91e9f7c06f..44dc22e706fb029a9c96f3bd125755fd55ac882b

...

(bunch of irrelevant stuff)
```

好的。不，但至少我还有一个提交杂凑，T2，来做一些 T21 做的事情。这是在我的宿敌`6799ddac675cab54060cdfb066dbfadb6708fc3f`使我的世界变得黑暗之前的最后一场好戏。我看了医生。我查阅了互联网。我又在黑暗中拍了一张照片:

```
> git branch -l rewrite-tests 44dc22e706fb029a9c96f3bd125755fd55ac882b
```

我在这里做的是创建一个名为`rewrite-tests`的新分支，根据 [git-branch 文档](https://git-scm.com/docs/git-branch)中指定的`git branch [--set-upstream | --track | --no-track] [-l] [-f] <branchname> [<start-point>]`模式，使用`44dc22e706fb029a9c96f3bd125755fd55ac882b`提交——即最后一个好的提交——作为其起点。我不确定`-l`选项是干什么用的，甚至不知道它是否有必要。有人说要用。耸肩。

然后，我将所有文件移出回购，并执行了以下操作之一:

```
git checkout rewrite-tests
```

git-checkout 命令将`HEAD`设置到指定的分支。换句话说，我告诉 git 我想在`rewrite-tests`分支上工作。然后，我只是把所有的文件复制回来，重新提交，让`restructure`分支枯萎死亡。

就这样，令我惊讶的是，我完了。最糟糕的时候已经过去了，而且还不算太早:我开始看到散列键。我有一个新的分支要开发，并且我的工作没有丢失(尽管中间的一些提交过早的死亡)。最终，我把所有东西都塞回了`master`，尽管我现在倾向于避免在任何回购中直接处理那个分支，以防这些[混乱](https://www.merriam-webster.com/dictionary/kerfuffle)再次出现。

我还是时不时去拜访[Blinky](https://www.youtube.com/watch?v=-Zb1lZgN9OE)女士，就是为了幸灾乐祸。实际上，不，我不这么做。但是如果你愿意的话，你可以亲自去看看我的修复报告:它包含了我的项目。这是我独自完成的大量代码。你可以想象当我想到我可能毁了它时我的感受。以及我想出解决办法时的感受。

在本文的开始，我提出损坏的 git 存储库——因为它是由离散的对象组成的——可以通过仔细的修复来恢复。我们已经看到这种行动的两种可能性。第一种，在损坏的斑点对象的情况下，是切除有问题的斑点，然后通过原始文件的重新散列在伤口上缝合。第二种方法是，如果第一种方法失败(或者如果问题是一个损坏的提交对象，而不仅仅是一个 blob ),则在损坏的地方切断受伤的分支，在残肢上移植一个新的分支，并重新提交该过程中受损的任何文件。

这些是不同的解决方案，但相似之处在于，它们都需要在相当低的级别上修复数据结构，即使只是操作文件。事实上，修复操作恰恰是可能的*，因为*git repo 被存储为一系列文件。鉴于文件系统实际上只是一个带有接口(命令行)的大型数据结构，git repo 也是一个类似文件系统的数据结构，带有接口、git 命令及其各种子命令和选项。如果您可以从命令行学习如何使用文件系统，换句话说，您也可以学习如何使用 git。

我希望这个故事已经解决了一套具体的指示，来解决你自己可能有一天会遇到的问题。不幸的是，我只有一些老生常谈的鼓励:你能做到！因为很难知道这类错误的原因，更不用说修复它们的最佳方法，所以也很难对它们进行归纳。你所能做的就是挖掘并反击熵。学习你的工具，不要害怕它们。如果只是为了体验，在你乞求你的一个[科学家朋友帮助你之前，自己尝试一些事情。只是记得先备份！](https://www.youtube.com/watch?v=nRnt3TE-V-Y)

如果这里有一个显而易见的寓意，那就是当你面对可怕的闪烁的状态栏或其迟钝的命令行等价物时，它会使恢复变得非常非常容易，成本也低得多:尽早提交，经常提交。[预防保健](https://blog.codinghorror.com/check-in-early-check-in-often/)毕竟，往往是最好的良药。