# 可变引用速度快吗？

> 原文：<https://medium.com/hackernoon/are-mutable-references-in-haskell-fast-f095f4144977>

我在剖析一个简单的循环(几年前)。它类似于:

```
countForever ref = forever $ modifyIORef' ref (+1)
```

我数了四个汇编指令。

> 加法需要大约 300 皮秒，但是有内存访问，所以让我们说两纳秒

为了描述，我写道:

```
iorefLoop = do
  ref <- newIORef (0 :: Int)
  replicateM_ 100000000 $ modifyIORef' ref (+1)
```

我预计这需要大约 0.2 秒，但是…

```
$ bench ./bin/ioref-loopbenchmarking ./bin/ioref-loop
time                 332.3 ms   (330.4 ms .. 333.8 ms)
                     1.000 R²   (1.000 R² .. 1.000 R²)
mean                 334.4 ms   (333.2 ms .. 335.9 ms)
std dev              2.822 ms   (2.182 ms .. 3.942 ms)
```

这是个好时机吗？我是不是对哈斯克尔有不切实际的期待？作为健全性检查，我用 C 写了一个类似的[程序](https://hackernoon.com/tagged/program):

```
int main (int argc, const char** argv)
{
    int ref = 0;
    while (ref < 100000000)
    {
        ref++;
    }
}
```

我编译并运行了它，如下所示:

```
$ gcc -O2 ./src/CLoop.c -o ./bin/CLoop && bench ./bin/CLoopbenchmarking ./bin/CLoop
time                 3.999 ms   (3.794 ms .. 4.235 ms)
                     0.980 R²   (0.972 R² .. 0.992 R²)
mean                 3.940 ms   (3.838 ms .. 4.093 ms)
std dev              593.3 μs   (422.8 μs .. 816.0 μs)
variance introduced by outliers: 90% (severely inflated)
```

这比我预料的要快得多。

> 也许`bench`在程序快时不准确？

我上了 IRC(不同的时间，因为 GHC 现在做代码更快):

```
jfischoff ~ Is there anyway to make modifyIORef' faster? 
jfischoff ~ I am surprised that in a second I was only able to 
          ↪ update this ref 100 million times: 
          ↪ timeout 1000000 $ forever $ modifyIORef' x (1+)
jfischoff ~ where as c++ was able to do the same in 4 milliseconds
glguy     ~ c++ was able to do 1 update every 0.04 nanoseconds?
glguy     ~ an update rate of 25 gigahertz?
dv-       ~ gcc probably just replaced it with a constant
jfischoff ~ dv-: perhaps
glguy     ~ That or C++ unlocks the fast mode of an Intel processor
```

燃烧。

```
$ gcc -O2 CLoop.c -S
```

组装如下:

```
; CLoop.s
; Notice, there are no jumps.
; There is no looping.
_main:                                  ## [@main](http://twitter.com/main)
 .cfi_startproc
## BB#0:
 pushq %rbp
Ltmp0:
 .cfi_def_cfa_offset 16
Ltmp1:
 .cfi_offset %rbp, -16
 movq %rsp, %rbp
Ltmp2:
 .cfi_def_cfa_register %rbp
 xorl %eax, %eax
 popq %rbp
 retq
 .cfi_endproc
```

`dv-`说得对。我猜编译器已经去掉了这个循环，因为我没有使用结果。我添加了一个`volatile`关键字来防止[优化](https://hackernoon.com/tagged/optimizations):

```
//CountForver.c
int main (int argc, const char** argv)
{
    volatile int ref = 0;
    while (ref < 100000000)
    {
        ref++;
    }
}
```

编译:

```
$ gcc -O2 ./src/CLoopVolatile.c -o ./bin/CLoopVolatile 
$ bench ./bin/CLoopVolatilebenchmarking ./bin/CLoopVolatiletime                 158.2 ms   (156.7 ms .. 159.5 ms)
                     1.000 R²   (0.999 R² .. 1.000 R²)
mean                 159.1 ms   (158.4 ms .. 160.2 ms)
std dev              2.423 ms   (1.755 ms .. 3.772 ms)
```

回路的组装是:

```
loop:                  
 incl -4(%rbp)         # Increment the value at the address stored        
                       # in rbp displaced by 4.
 movl -4(%rbp), %eax   # move the value at the address stored in rbp 
                       # displaced by 4 to eax.
 cmpl $100000000, %eax # Compare to 100000000 to eax.
 jl loop               # Jump to the start of the loop if the eax 
                       # was less than when compared above.
```

所以，递增 C 的可变 refs 比`IORef`快两倍多？不过，也许手写汇编会更快？也许`volatile`这个关键词把优化抛得太远了？

```
; AsmLoop.asm
EXTERN _exit
GLOBAL start

SECTION .data
  align 8
  iterationCount DD 100000000
  result DD 0

SECTION .text
start:
   ; Copy the iteration count to the ecx register
   ; which is used by the loopnz instruction
   mov ecx, [iterationCount]

loopBlock:  
   inc dword [result] ; Increment the value at the address of result
   loopnz loopBlock   ; Decrement the ecx counter and loop until ecx 
                      ; is zero

exitBlock:
   push dword 0 ; Set the exit code to zero
   mov  eax, 1  ; Place the system call number (exit) in the eax reg
   sub  esp, 4  ; I have to add some dummy space to the stack for 
                ; some reason
   int  0x80    ; Exit
```

编译:

```
$ nasm -fmacho src/AsmLoop.asm 
$ ld -o ./bin/AsmLoop -macosx_version_min 10.7.0 ./src/AsmLoop.o 
$ bench ./bin/AsmLoop

benchmarking ./bin/AsmLoop
time                 185.2 ms   (183.7 ms .. 186.9 ms)
                     1.000 R²   (0.999 R² .. 1.000 R²)
mean                 187.0 ms   (185.9 ms .. 188.5 ms)
std dev              3.132 ms   (2.229 ms .. 4.269 ms)
```

哇，我的 l33t skillz 增加了大约 15%的时间(三年前这个版本更快…只是说)。

所以`IORef`比 C 和汇编语言中的简单方法慢两倍。怎么回事？到核心！

```
$ stack exec ghc-core -- --no-syntax -- -O2 \ 
      -dsuppress-all src/IORefLoop.hs
```

透过核心我看到:

```
...
case readMutVar# ipv1_aVW w2_a35m
of _ { (# ipv2_aWt, ipv3_aWu #) ->
case ipv3_aWu of _ { I# x_aUW ->
case writeMutVar# ipv1_aVW (I# (+# x_aUW 1#)) ipv2_aWt
...
```

我发现 GHC 核心几乎不可读。

一个技巧是尽量忽略大部分 case 语句。第一个和第三个 case 语句不是为了检查备选方案，而是为了确保 IO 操作的正确排序。此外，重命名生成的变量也很有帮助。

以下是上述内容的清理版本:

```
...
case readMutVar# ioRef token of
  { (# token', x #) -> 
case x of
  { I# unbox -> case writeMutVar# ioRef (I# (+# unbox 1#)) token'
...
```

第二个 case 语句将一个`Int`取消装箱为一个原始的取消装箱的`Int#`，

```
case x of
  { I# unbox 
```

然后设置时装箱。

```
case writeMutVar# ioRef (I# (+# unbox 1#)) token'
```

`I#`是`Int`构造函数(`#`意味着它是一个编译器原语)。它包装或装箱一个未打包的、未装箱的“机器”int。大多数时候，GHC 可以自动取消原语的装箱，但在这种情况下它不能。装箱/拆箱可能是经济放缓的根源。

我们需要一个未装箱的可变引用。我能想到两个选项: [Ptr Int](https://hackage.haskell.org/package/base-4.7.0.1/docs/Foreign-Ptr.html) 和 [MutableByteArray](http://hackage.haskell.org/package/primitive-0.5.3.0/docs/Data-Primitive-ByteArray.html#t:MutableByteArray) 。

第`Ptr`版:

```
main = alloca $ \ptr -> do
   poke ptr (0 :: Int)
   replicateM_ 100000000 $ do
     i <- peek ptr
     let !i' = i + 1
     poke ptr i'
```

跑步时，我得到:

```
bench ./bin/ptr-loopbenchmarking ./bin/ptr-loop
time                 175.2 ms   (174.1 ms .. 176.2 ms)
                     1.000 R²   (0.999 R² .. 1.000 R²)
mean                 174.7 ms   (174.1 ms .. 175.2 ms)
std dev              1.626 ms   (1.372 ms .. 2.089 ms)
```

这很好！几乎快了一倍`IORef`。

现在的`MutableByteBuffer`版本:

```
main = do
  mba <- newAlignedPinnedByteArray 4 8
  replicateM_ 100000000 $ do
      i <- readByteArray mba 0 :: IO Int
      let !i' = i + 1
      writeByteArray mba 0 i'
```

跑步:

```
bench ./bin/mutable-byte-buffer-loopbenchmarking ./bin/mutable-byte-buffer-loop
time                 175.2 ms   (173.5 ms .. 177.3 ms)
                     0.999 R²   (0.999 R² .. 1.000 R²)
mean                 177.4 ms   (176.5 ms .. 178.4 ms)
std dev              2.423 ms   (1.987 ms .. 2.919 ms)
```

基本一致。

我有进步，但是我不想用`Ptr`或者`MutableByteBuffer`。它们不是很安全(`Ptr`可以说是足够安全了，但如果可以的话，我宁愿不要直接手动分配内存)。

如果其中一个类型有一个安全的包装就好了。有！`Vector`是围绕`MutableByteBuffer`的安全包装。下面是`Vector`版本:

```
main = do
  mba <- M.new 1
  replicateM_ 100000000 $ do
      i <- M.read mba 0 :: IO Int
      let !i' = i + 1
      M.write mba 0 i'
```

跑步:

```
bench ./bin/vector-loopbenchmarking ./bin/vector-loop
time                 178.6 ms   (177.3 ms .. 179.9 ms)
                     1.000 R²   (0.999 R² .. 1.000 R²)
mean                 178.2 ms   (177.1 ms .. 179.3 ms)
std dev              3.021 ms   (2.082 ms .. 4.288 ms)
```

不错！只需增加很少的时间，我们就可以获得一个快速的、未装箱的可变引用的安全接口。对单个元素使用集合类型有点奇怪，但也不可怕。

# 桌子上有什么

`CLoopVolatile`和`AsmLoop.asm`不是编写递增一个数的循环的最快方法。这将涉及到在循环期间将引用放在寄存器中，以及像循环展开这样的其他技巧。这里有一个简单的汇编例子，可以让你感受一下速度有多快。

```
; FastAsmLoop.asm
EXTERN _exit
GLOBAL startSECTION .text
start:
   mov rcx, 0
   mov rax, 100000000loopBlock:
   inc qword rcx     ; 
   cmp rcx, rax      ; 
   jl loopBlock      ; exitBlock:
  mov     rax, 0x2000001 ; exit
  mov     rdi, 0
  syscall
```

收集

```
$ nasm -fmacho64 src/FastAsmLoop.asm 
$ ld -o ./bin/FastAsmLoop -macosx_version_min 10.7.0 \ 
      ./src/FastAsmLoop.o 
$ bench ./bin/FastAsmLoop;benchmarking ./bin/FastAsmLoop
time                 38.38 ms   (37.96 ms .. 39.15 ms)
                     0.999 R²   (0.995 R² .. 1.000 R²)
mean                 38.22 ms   (37.98 ms .. 38.87 ms)
std dev              715.8 μs   (282.6 μs .. 1.261 ms)
```

我几乎不相信这些数字，但我认为这表明 GHC 仍然有一些表现。

另外值得注意的是，在 Haskell 中，这并不是一个紧密循环的写法。人们不需要可变的引用。在以后的文章中，我想对 Haskell 和 assembly 做更直接的比较。C 代码实际上只是一个基线，引导我去研究 GHC 在做什么。

*更新:Michael Snoyman 提醒我他的一个包提供了一个未装箱的可变引用，以及其他一些好东西:*[*https://www . stack age . org/haddock/lts-8.16/mutable-containers-0 . 3 . 3/Data-mutable . html # t:URef*](https://www.stackage.org/haddock/lts-8.16/mutable-containers-0.3.3/Data-Mutable.html#t:URef)

包含该代码的项目也可以产生这些计时，可以在这里找到:[https://github . com/jfischoff/are-mutable-references-in-haskell-fast](https://github.com/jfischoff/are-mutable-references-in-haskell-fast)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！