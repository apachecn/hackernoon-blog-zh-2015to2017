# 优化 Michelson 中的堆栈操作

> 原文：<https://medium.com/hackernoon/optimizing-stack-manipulation-in-michelson-31ba7ff11a3a>

这篇文章描述了一个简单的编译步骤来帮助 Michelson 中的堆栈操作。这对于发布来说并不重要，所以我们近期不会关注它，但我确信这个问题会让一些人感到厌烦，我也很乐意看到有人接手这个问题。

![](img/a232adfdedcfda65ba6afaf6a07e6131.png)

Rubik’s cube — photo by keqs

当用迈克尔逊(Tezos 的智能合同语言)写合同时，栈操作经常会碍事。为了获得一种感觉，看看米洛的[合同示例](https://www.michelson-lang.com/)。对程序员来说，拆分对、重组对、重新排序堆栈的元素非常耗时，并且降低了代码的可读性。

幸运的是，有三种解决方法:

1.  为 Emacs 使用迈克尔逊 IDE，可以看到它的原型[迈克尔逊模式](https://github.com/tezos/tezos/blob/master/emacs/michelson-mode.el)。这种模式为您提供了光标处堆栈状态的符号表示。随着注释的引入，这一点将会得到改善，让您可以用标识符“标记”堆栈上的值。
2.  用编译成迈克尔逊的高级语言编写程序，比如 OCamlPro 的 [liquidity](https://github.com/OCamlPro/liquidity) 。
3.  只对堆栈操作使用简单的编译步骤

# 混乱宏

我考虑 3 已经有一段时间了。我称之为“混杂”指令，它是这样工作的。假设您有一个如下形式的堆栈:

```
(a,b):c:(d,(e,f))
```

想要一叠表格

```
(a,f):(c,d)
```

你可以写作

```
CAR; DIP { SWAP; DUP; CAR; SWAP; CDR; CDR;}; PAIR; DIP {SWAP; PAIR; PUSH;}
```

这不是很明确…或者你可以写

```
JUMBLE (a,b):c:(d,(e,f)) => (a,f):(c,d)
```

这样就清楚多了！当然，混杂操作需要编译——我们马上会谈到——但是请注意，它也可以很容易地被反编译。

任何形式的代码序列(DUP | DROP | SWAP | CDR | CAR | DIP | PAIR)*都可以压缩成一条混杂指令。

# 汇编混乱

写下一个正确的 JUMBLE 实现并不简单，但仍然相对简单。一种简单的方法是首先拉平初始堆栈和目标堆栈中的所有线对。通过重复应用 DUP 形式的不成对宏来展平初始堆栈对；CDR 互换；汽车。然后对展平的初始堆栈进行排序，并复制变量以匹配展平的目标堆栈。然后重组这些对以匹配目标堆栈。

这不是一种非常有效的做事方式。一些对可能同时存在于初始堆栈和目标堆栈中，断开它们并重新组合它们可能是低效的。此外，在最坏的情况下，只能够交换附近条目的排序元素必然是二次的。

可以从这个正确的过程开始，通过找到共同的模式和避免不必要的堆栈操作，进行一系列的优化。

另一种方法是直接搜索最优解。我已经实现了一个编译的原型，最近把它放到了 Github 上:[https://github.com/tezos/optimal_stack](https://github.com/tezos/optimal_stack)

这是一个 [A*](https://en.wikipedia.org/wiki/A*_search_algorithm) 的实现，用一些启发法来缩小搜索空间。它没有经过任何方式的性能调优，因此对大型堆栈操作没有帮助，但它会发现一些重要的模式。

我推荐在 [utop](https://opam.ocaml.org/blog/about-utop/) 试试，可以这样做

```
$ ocamlc -c *.ml *.mli
utop
#require "heap";;
#require "optimal_stack";;
open Optimal_stack;;
example ()
```

示例优化了以下问题

```
JUMBLE (a, b):c => (c,a):b
```

可以这样编程

```
**let** a **=** **Var** **"a"** **and** b **=** **Var** **"b"** **and** c **=** **Var** **"c"** **in** 
**let** start  **=** **Stack** **([Pair** **(**a, b**);** c**]**, **[])** **and** target **=** **Stack** **([Pair** **(**c,a**);** b**]**, **[])**
 **in** optimize start target
```

给定的解决方案是

```
DUP; POP; CAR; SWAP; PAIR; PUSH; CDR; SWAP
```

你会注意到堆栈类型是一对列表。这让我们可以将 DIP 操作表示为辅助堆栈上的 PUSH 和 POP 操作。除非你在一个 DIP 里面，否则辅助堆栈应该是空的。

# 后续步骤

我的重点是启动网络，我们不需要混乱。然而，我认为这很酷，我很乐意看到有人接手前期工作，使其更快，并能够解决更大的优化问题。

我对如何进一步发展的建议是:

1.  找到更多关于从一个堆栈移动到另一个堆栈的成本的“定理”,这些定理可以用在 A*启发式算法中。
2.  使用次优搜索。如果你能保证你的定理的误差范围，你就能保证这个解有多接近最优解。
3.  将问题代入传统的约束优化求解器。
4.  不要盲目地寻找解决方案，从一个已知的解决方案开始(遵循前面概述的方法),并搜索该解决方案的可证明正确的改进。
5.  将问题数学形式化，并通过代数推理推导出计算最优解的有效算法，这与通过群论将魔方数学化没有什么不同。我认为这有 10%的机会成功，但如果成功了，那就是很高的回报了。