# 广义类型约束

> 原文：<https://medium.com/hackernoon/generalized-type-constraints-b256e79dd266>

在这篇短文中，我将展示 Scala 中的通用类型约束——它们是什么，为什么我们需要它们，以及它们是如何使用的。

首先，让我向您展示两个 Scala 难题。

## 第一个难题:

```
def pair[S](s1: S, s2: S): (S, S) = (s1, s2)
```

如果我们向这个方法传递两个不同类型的值，你认为会发生什么？让我们看看:

```
scala> pair(“foo”, 1)
res0: (Any, Any) = (foo,1)
```

发生了什么，编译器说“好的，第一个值是一个字符串，第二个值是一个 Int，但这实际上是行不通的，因为我需要这两个参数是相同的类型。我现在将尝试找到它们最近的公共超类型。给你——随便什么都行。酷，这些参数中的每一个也都是 Any，所以我将它们视为 Any 类型，以满足类型约束。

顺便说一下，如果我们有一个 S 型的界限，例如:

```
def pairVals[S <: AnyVal](s1: S, s2: S) = (s1, s2)
```

然后用 String 和 Int 调用该方法会给我们一个编译错误，因为其中一个参数不是 AnyVal 的子类型(记住 String 是 AnyRef 的子类型，而不是 AnyVal)。编译器说“你给我的参数的最接近的公共超类型是 Any，但是这违反了给定的类型约束”:

```
scala> pairVals(“foo”, 4)
<console>:12: error: inferred type arguments [Any] do not conform to method pairVals’s type parameter bounds [S <: AnyVal]
```

返回到 pair()方法。这里有一个问题——如果我们想说“方法对()应该接受两个**相同类型**的参数”呢？我们看到 pair()很容易允许我们传入一个 String 和一个 Int 编译器将简单地推断出您传递了 Any 类型的值。如果我们希望只允许两个参数共享完全相同的类型(例如，两个 Int 或两个 String，但不是一个 Int 和一个 String)的调用，该怎么办？

在我回答这个问题之前，还有一个难题:

## 第二个难题:

```
def advPair[S <: T, T](s: S, t: T): (S, T) = (s, t)
```

这是在稍微不同的情况下的相同原理。当我们用两个不同的参数调用这个方法时，你认为会发生什么？

```
scala> advPair(“foo”, 1)
res0: (String, Any) = (foo,1)
```

乍一看，您可能会认为 advPair()有两个参数，第一个是 second 的子类型，因此用 String 和 Int 调用它会失败(因为 String 不是 Int 的子类型)。同样，你也错了。就像在第一种情况下，编译器将试图解决混乱，看看它是否可以通过向上转换您的值来满足类型约束。*(顺便说一句，不要介意我使用“向上投射”这个术语——我知道这听起来有点丑陋，但你我都知道没有什么丑陋的事情发生。Int *是* an，就像 Scala 中的其他东西一样)。*

所以是的，我们传入了一个字符串和一个 Int，但是 Int 也是一个 Any。当我们从这个角度看问题时，一切都解决了——我们传递了一个字符串和一个 Any，并且满足了第一个参数是第二个参数的子类型的条件。

那么，我们怎样才能在“原始”类型上设置约束，而不使编译器试图变得慷慨并向上传递我们的参数，直到类型约束得到满足？

## 答案:

通过使用*广义类型约束*。

第一个谜题解决了:

```
def pair[S, T](s: S, t: T)**(implicit ev: S =:= T)**: (S, T) = (s, t)
```

第二个谜题解决了:

```
def advPair[S, T](s: S, t: T)**(implicit ev: S <:< T)**:(S, T) = (s, t)
```

看到那些隐式参数了吗？他们是我们困境的解决方案。你可以把它们看作类型:语句 S =:= T 与 Map[S，T]具有相同的性质。只是广义类型约束(“GTC”)是内嵌的，而不是前缀的。

那么当你有一个 GTC 时会发生什么呢？好吧，编译器会施展它的推理魔法，就好像 GTC 不存在一样。一旦一切都解决了，它将检查 GTC 是否满意。看看在 pair()例子中我们如何不再有两个 S 类型的实参，而是不同类型的实参，返回类型也从(S，S)变成了(S，T)？这样，编译器不需要做任何向上转换的魔术——它只是将给定的参数配对成一个元组。但是，在处理方法体之前，它将检查 GTC 是否满意；也就是说，类型 S 和 T 实际上是否是同一类型。

```
scala> pair(“foo”, 1)
<console>:12: error: Cannot prove that String =:= Int.
 pair(“w”, 1)
 ^scala> pair(“foo”, “foo”)
res0: (String, String) = (w,a)
```

第二个方法也经历了类型约束方面的一些变化——我们删除了[S <: t="" part.="" why="" again="" to="" prevent="" the="" compiler="" from="" doing="" any="" upcasting="" magic.="" we="" saw="" how="" having="" caused="" upcast="" int="" into="" in="" order="" conform="" type="" constraint="" right="" well="" now="" will="" happily="" realize="" that="" there="" are="" no="" constraints="" which="" means="" s="" and="" can="" be="" string="" without="" problems="" so="" it="" proclaim="" its="" final="" decision:="" is="" int.="" but="" then="" gtc="" kicks="" tells="" you="" resolved="" concrete="" types="" here="" my="" demands="" if="" not="" a="" subtype="" of="" invocation="" fail.=""/>

```
scala> advPair(“foo”, 1)
<console>:12: error: Cannot prove that String <:< Int.
 advPair(“foo”, 1)
 ^scala> val any: Any = "any"
any: Any = anyscala> advPair("foo", any)
res0: (String, Any) = (foo,any)
```

## Conclusion

So here’s the summary:

Compiler will try to conform to type bounds by upcasting the types as needed, trying desperately to find a combination that works. If the process fails, compiler will cry. If it succeeds, compiler will assign each generic type parameter a concrete type (e.g. S and T will become String and Int).

Then, when the types are resolved, compiler will see that an implicit GTC is needed and it will provide it. Note that when you are writing a method that needs the GTC, you just need to declare it as an implicit parameter and that’s it. Compiler will provide the needed GTC at compile time and plug in the resolved types, issuing an error message in case GTC is not satisfied.

Fun with types never ends in Scala. :) As usual, contact me on sinisalouc@gmail.com with feedback or [在 Twitter 上找到我。](https://twitter.com/sinisalouc)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！