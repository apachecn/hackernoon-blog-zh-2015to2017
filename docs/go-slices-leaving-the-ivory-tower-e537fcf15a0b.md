# 去切片——离开象牙塔

> 原文：<https://medium.com/hackernoon/go-slices-leaving-the-ivory-tower-e537fcf15a0b>

这些天来，我更喜欢的编程环境是一个名为 [Pharo](http://pharo.org/) 的 [Smalltalk-80](https://en.wikipedia.org/wiki/Smalltalk) 后代。该系统允许我快速探索想法，更改代码，我总是惊讶于我需要这么少的代码来完成事情。我使用环境这个词，因为编程语言只是它的一部分。正是编译器、编辑器、调试器、检查器的组合决定了一个人解决问题的速度有多快，我有时会痛苦地想起 Pharo 领先了多少，尤其是在编写代码时，这些代码解决了*编辑、编译、运行、* ***等待*** *、崩溃、添加 printf、重复、* ***等待*** *、在相同位置崩溃但有更多信息、周期*的问题。

但是我想得太多了。我在初中的时候开始了我的编程生涯，当时我将命令式代码的第一行输入到一个连接到学校 Unix 系统的终端上。我们使用了一种叫做 ELAN 的语言，解决了一些问题，比如寻找质数、构建猜谜游戏等等..随后出现了其他命令式语言，有一段时间我用 Java 写东西，但后来开始主要阅读 T21，写 C++和一点 C 语言。在大学里，我接触了大量的 Haskell，从那以后我用 Python 写脚本，最近几年我学习了 Smalltalk。

Smalltalk-80 确实走在了时代的前面。它开始于一个打赌，即一种语言的语义可以在一张纸上描述，它运行在利用摩尔定律的计算机上。这意味着施乐帕洛阿尔托研究中心制造的非常昂贵的计算机代表了由于摩尔定律，在短短几年内就可以买得起的东西。问题是，想使用 Smalltalk-80 的人不能等到 90 年代，它被困在象牙塔里，成为拥有昂贵电脑和雄厚财力的精英们的语言。

我不时尝试离开我个人的象牙塔，探索其他编程语言。我研究 Erlang、Rust、Go，在过去的几周里，我一直在调试、修复和编写 Go 代码，以了解这种语言的起伏变化。今天我想写的是如何推理运行时故障，以及语言/运行时可以提供什么来提高效率。

我正在解析一个网络协议，它告诉我在哪里可以找到下一个报头。在 Go 中，消息可以用一个 *[]uint8* (一个无符号 int 数组，每个数组取 8 位，在 Go 中从左到右读取类型)来表示，我写了这样一些基本的东西:

```
func NextHeader(message []uint8) []uint8 {
   offset := message[2]
   len := message[2 + offset]
   return message[2 + offset + 1 : len]
}
```

经验丰富的 Python 和 Go 程序员会立即发现问题，但在运行生成的应用程序时，它有时会工作，但对于某些消息，它会中止:

```
panic: runtime error: slice bounds out of rangegoroutine 1 [running]:main.NextHeader(...) slicetest.go:8
```

恐慌缺乏关于哪个部分和什么界限的信息。因为这只发生在我解析的一些消息上，所以我采取了调试的方法，比如:

```
func printSliceBounds(message []uint8, begin, end uint8) {
    if recover() != nil {
       fmt.Printf("%#v[%v:%v]\n", message, begin, end)
    }
}func NextHeader(message []uint8) []uint8 {
   offset := message[2]
   len := message[2 + offset]
   defer printSliceBounds(message, offset, len)
   return message[2 + offset + 1 : len]
}
```

这将确保在 *NextHeader* 之后和返回到调用者之前 *printSliceBounds* 函数将被调用。如果代码正在从崩溃中恢复，格式化的切片和边界将被打印出来。我想用这个切片构建一个测试用例，但是当我试图编译代码时，Go 编译器的静态分析指出了错误。

在此之前，这可能对你来说都是相当标准的，但我很高兴在象牙塔中工作。所以让我告诉你我来自哪里。在我的世界里，一切都是物体。这包括正在运行的执行的 stackframe/activation 记录，并由保留关键字 *thisContext* 引用。它是一个像系统中任何其他对象一样的对象，可以被查询和**操作**。我可以查询程序计数器，我可以从堆栈中读取对象，我可以遍历调用层次结构，我可以访问当前正在执行的方法的抽象语法树(方法只是另一个对象),但是我还可以修改程序计数器、上下文链等。

Smalltalk 一个切片错误将被作为异常发出信号。与许多其他语言相比，异常处理代码不是虚拟机的一部分，而是运行时包的一部分，构建在 *thisContext* 之上。在开发时，我会在我的环境中安装一个调试器，让我检查切片，但在生产中运行时，我可能会考虑将异常和整个运行过程序列化到一个文件中。在 Lambda AWS 上运行 Pharo，然后“崩溃”并在本地调试，这是一个很好的演示。

作为一个思想实验，你可以问自己如何在 C 中实现一个调试器来运行一个暂停的方法直到它结束，以及 Smalltalk 中的实现会是什么样的？系统的美妙之处在于，通过正确的抽象集合，人们可以构建通常非常耗时的东西。在 Smalltalk 中，最简单的方法是创建一个新的上下文，放入一个 trap/halt/breakpoint/continuation，并将其放在当前方法和原始调用者之间，然后继续执行流程。[在这里](http://git.savannah.gnu.org/cgit/smalltalk.git/tree/packages/debug/DebugTools.st?h=3.2.5#n203)你可以看到一个真实的实现使用这个为 [GNU Smalltalk](http://smalltalk.gnu.org/) 。

异常处理有它自己的问题，Go 为错误传播和恐慌选择了一条更明确的路径是公平的。我开始检查添加一个携带切片和边界的高级出界对象需要什么，并研究了 Go 源代码。好的一面是整个系统构建得非常快(例如，与 gcc/libstdc++相比)。这是我研究后的发现。第一个是编译器生成边界检查的地方:

```
In src/cmd/compile/internal/gc/ssa.go
// slice computes the slice v[i:j:k] and returns ptr, len, and cap of result.
// i,j,k may be nil, in which case they are set to their default value.
// t is a slice, ptr to array, or string type.func (s *state) slice(t *types.Type, v, i, j, k *ssa.Value) (p, l, c *ssa.Value) {... s.sliceBoundsCheck(i, j)
   if j != k {
     s.sliceBoundsCheck(j, k)
   }
   if k != cap {
     s.sliceBoundsCheck(k, cap)
   }
...
```

编译器将为您的切片代码调用生成多达三次检查。 *i* ， *j* ， *k* 由用户提供，可以为空，然后将使用默认值 *zero (i)* ， *len (j)* 和*cap(k】*。将始终生成第一次检查，检查 *0 ≤ i ≤ j* ，如果生成第二次检查，将检查 0 ≤ j ≤ k(长度≤容量)，最后一次检查容量≤现有容量。

简单支票的 ssa 可能如下所示:

```
func Slice(slice []uint8, start, end int) []uint8 {
   return slice[start:end] // to defeat the static analysis
}Slice <nil>b1:
v1 = InitMem <mem>
v9 = Arg <int> {start} : start[int]
v10 = Arg <int> {end} : end[int]
v28 = Arg <int> {slice} [16] : slice+16[int]
v32 = Arg <*byte> {slice} : slice[*byte]
--- Load registers and check 0 <= i <= j here.
v7 = LoadReg <int> v9 : AX
v15 = LoadReg <int> v10 : CX
v31 = CMPQ <flags> v7 v15
ULE v31 -> b2 b3 (likely)
b2: <- b1
--- Load k into register and check 0 <= j <= k here.
v34 = LoadReg <int> v28 : DX
v8 = CMPQ <flags> v15 v34
ULE v8 -> b4 b3 (likely)
b4: <- b2
... create the new slice here and return it--- Error case jumped to from b1 (0 <= i <= j) or b2 (0 <= j <= k)
b3: <- b1 b2
v18 = CALLstatic <mem> {runtime.panicslice} v1
Exit v18
```

在 *panicslice* 被调用时，关于切片的信息以及三次检查中哪一次失败的信息都消失了。经过一番探索后，我可以将错误类型传递给 *panicslice* ，以及切片和边界。第一个问题是替换手动公共子表达式消除(CSE ),它假设每一行代码只能有一种恐慌，但后来我面临了一个基本问题。src/runtime/panic.go 处于食物链的最底层，我不能只使用 *fmt。Errorf* 在那里格式化一个新的错误。首先，系统可能内存不足，其次是 fmt。Errorf 可能会使用损坏的切片代码本身，然后死机代码会重复出现在更多的死机中。

那时，我探索了是否可以在外部使用 defer 作为通用的错误处理程序。想法是在 *main()* 中安装一些东西，它将打印错误代码中的出错部分和边界。它将使用 *recover()* 并以某种方式检查它是否通过了 *panicslice* ，然后以某种方式找到变量，然后甚至可能为测试用例创建样板文件。

虽然 Go 有内置的反射和函数代码的程序计数器，但它似乎没有相同的数据(堆栈指针)。与 Smalltalk *thisContext* 相比，Go 给了我们静态信息(函数名、行号)，但缺少动态信息(堆栈中的变量)。理论上它可以被添加(IIRC 的调试格式 DWARFv2 支持跟踪变量的位置)，但是它似乎没有在 *runtime/symtab.go.* 中使用

我最后做了一个概念验证，并像这样修补编译器:

```
// Panic if slice indices are not in bounds.s.sliceBoundsCheck(i, j, v, i, j, s.constInt(types.Types[TINT], 1))
if j != k {
 s.sliceBoundsCheck(j, k, v, j, k, s.constInt(types.Types[TINT], 2))
}
if k != cap {
 s.sliceBoundsCheck(k, cap, v, k, cap, s.constInt(types.Types[TINT], 3))
}
```

以及类似的运行时代码。使 SliceError 成为错误字符串，但提供有关错误的高级信息:

```
type SliceError struct {
  errorString
  Slice interface{}
  Start int
  End int
  ErrorKind int
}func panicslice(slice interface{}, len int, start int, end int, kind int) {
  panicCheckMalloc(sliceError)
  panic(SliceError{
           errorString: sliceError.(errorString),
           Slice: slice,
           Start: start,
           End: end,
           ErrorKind: kind})
}
```

使用延迟/恢复方法，我可以得到如下输出:

```
runtime.SliceError{errorString:"slice bounds out of range", Slice:<nil>, Start:10, End:6, ErrorKind:2}
```

这里我试图将长度为 6 的数组切到第 10 位。这导致第二次检查失败，并且在将切片 ptr 放入堆栈时出现错误(它被格式化为 nil)。在我看来，这比打印“切片边界超出范围”更有帮助(编写 foo 并打印行号将已经指向切片)。

下一步是什么？因此，概念验证是可行的，但问题是产生了更多的代码。这包括为 *panicslice* 代码设置堆栈，并为发出的每个检查设置一个错误路径。我觉得上行围棋代码不实用。它在调试标志后面可能很有用。我认为有用的是有一个类似于 thisContext 的概念，允许访问数据。这样，错误处理程序可以从堆栈或寄存器中提取切片和边界。你是怎么想的，如何调试你的围棋程序？如何在生产中捕获坏数据？