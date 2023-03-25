# 应对(代码)失败

> 原文：<https://medium.com/hackernoon/coping-with-code-failures-f15e60d07bb7>

异常处理很烦人。如果世界上的一切都按照它应该的方式运行，那就完全没有必要了。当然，这只是一种幻想。哈斯克尔不能改变现实。但是它的错误设施比大多数语言好得多。本周我们将看看一些常见的错误处理模式。我们将看到几个 Haskell 代码更简单、更清晰的例子。

# 什么都不用

Haskell 中最基本的例子是`Maybe`类型。这允许我们封装任何有可能失败的计算。为什么这比其他语言的类似想法要好？让我们以 Java 为例。当你处理指针类型时，封装`Maybe`很容易。你可以使用“空”指针作为你的失败案例。

```
public MyObject squareRoot(int x) {
  if (x < 0) {
    return nil;
  } else {
    return MyObject(Math.sqrt(x));
  }
}
```

但是这有几个缺点。首先，空指针(一般来说)看起来和类型检查器的常规指针一样。这意味着编译时不能保证你处理的任何指针都不为空。想象一下，如果我们必须把每个 Haskell 值都放在一个 Maybe 中。我们需要不断地解开它们，否则就有触发“空指针异常”的风险。在 Haskell 中，一旦我们处理了`Nothing`的情况，我们就可以传递一个纯值。这使得其他代码知道它不会抛出随机错误。考虑这个例子。我们检查我们的指针已经在`function1`中非空一次。尽管如此，良好的编程实践要求我们在`function2`中执行另一项检查。

```
public void function1(MyObject obj) {
  if (obj == null) {
    // Deal with error
  } else {
    function2(obj);
  }
}public void function2(MyObject obj) {
  if (obj == null) {
    // ^^ We should be able to make this call redundant
  } else {
    // …
  }
}
```

当我们处理非指针的原始值时，第二个棘手的问题出现了。我们往往没有很好的办法来处理这些情况。假设您的函数返回一个`int`，但是它可能会失败。你如何代表失败？通过使用“哨兵”值(如 0 或-1)来处理这种情况并不少见。

但是如果你的函数范围跨越了所有的整数，你就有点困在那里了。如果使用枚举类型，代码可能看起来更干净，但这并不能避免问题。如果 null 在特定的上下文中是有效的，那么指针值也会出现同样的问题。

```
public int integerSquareRoot(int x) {
  if (x < 0) {
    return -1;
  } else {
    return Math.round(Math.sqrt(x));
  }
}public void (int a) {
  int result = integerSquareRoot(a);
  if (result == -1) {
    // Deal with error
  } else {
    // Use correct value
  }
}
```

最后，Haskell 中带有`Maybe`的一元复合更加自然。Java 代码中有很多这种意大利面条的例子:

```
public Result computation1(MyObject value) {
  …
}public Result computation2(Result res) {
  …
}public int intFromResult(Result res) {
  …
}public int spaghetti(MyObject value) {
  if (value != null) {
    result1 = computation1(value);
    if (result1 != null) {
      result2 = computation2(result1);
      if (result2 != null) {
        return intFromResult(result2);
      }
    }
  }
  return -1;
}
```

如果我们太天真，我们可能会得到一个不那么漂亮的版本:

```
computation1 :: MyObject -> Maybe Result
computation2 :: Result -> Maybe Result
intFromResult :: Result -> Intspaghetti :: Maybe MyObject -> Maybe Int
spaghetti value = case value of
  Nothing -> Nothing
  Just realValue -> case computation1 realValue of
    Nothing -> Nothing
    Just result1 -> case computation2 result1 of
      Nothing -> Nothing
      Just result2 -> return $ intFromResult result2
```

但是正如我们在我们的第一篇[单子文章](https://mmhaskell.com/blog/2017/2/13/finally-understanding-monads-part-1)中讨论的，我们可以让它更干净。我们将在`Maybe`单子中组合我们的动作:

```
cleanerVersion :: Maybe MyObject -> Maybe Int
cleanerVersion value = do
  realValue <- value
  result1 <- computation1 realValue
  result2 <- computation2 result1
  return $ intFromResult result2
```

# 使用任何一种

现在假设我们想让我们的错误包含更多的信息。在上面的例子中，如果失败，我们将输出`Nothing`。但是调用该函数的代码将无法知道错误究竟是什么。这可能会妨碍我们的代码纠正错误的能力。我们也没有办法向用户报告具体的失败。正如我们已经探索过的，Haskell 对此的回答是`Either`单子。这允许我们附加任何类型的值作为可能的失败。在这种情况下，我们将改变每个函数的类型。然后，我们将更新函数，使用描述性的错误消息，而不是返回`Nothing`。

```
computation1 :: MyObject -> Either String Result
computation2 :: Result -> Either String Result
intFromResult :: Result -> InteitherVersion :: Either String MyObject -> Either String Int
eitherVersion value = do
  realValue <- value
  result1 <- computation1 realValue
  result2 <- computation2 result1
  return $ intFromResult result2
```

现在假设我们想在 Java 中实现这一点。我们如何做到这一点？我知道有几个选择。都不是特别有食欲。

1.  弹出故障条件时打印错误信息。
2.  当失败条件出现时，更新一个全局变量。
3.  创建可能包含错误值或成功值的新数据类型。
4.  向函数添加一个参数，如果出现故障，该参数的值将被填入一条错误消息。

第一对夫妇依赖于任意的副作用。作为 Haskell 程序员，我们不喜欢这些。第三种选择需要弄乱 Java 的模板类型。这些类型比 Haskell 的参数化类型更难处理。如果我们不采用这种方法，我们需要为每个不同的返回值创建一个新的类型。

最后一个方法有点反模式，弥补了元组在 Java 中不是第一类构造的事实。检查你的一个**输入**值作为一个**输出**结果是非常反直觉的。有了这些选择，我随时都可以找到哈斯克尔。

# 使用异常和处理程序

既然我们已经理解了代码中处理错误的更“纯粹”的方式，我们就可以处理异常了。异常出现在几乎每一种主要的编程语言中；哈斯克尔也不例外。Haskell 的`SomeException`类型封装了可能的失败条件。它可以包装任何属于[异常](https://hackage.haskell.org/package/base-4.9.1.0/docs/Control-Exception.html)类型类成员的类型。您通常会创建自己的异常类型。

通常，当我们想要声明代码执行路径失败时，我们抛出异常。我们将允许完全不同的代码来处理错误，而不是向调用函数返回一些值。如果这听起来令人费解，那是因为它确实如此。一般来说，你希望保持控制流尽可能清晰。有时虽然我们无法避免。

假设我们正在调用一个函数，我们知道这个函数可能会抛出一个异常。我们可以通过附加一个处理程序来“处理”这个异常。在 Java 中，您可以这样做:

```
public int integerSquareRoot(int value) throws NegativeSquareRootException {
  ...
}public int mathFunction(int x) {
  try {
    return 2 * squareRoot(x);
  } catch (NegativeSquareRootException e) {
    // Deal with invalid result
  }
}
```

要在 Haskell 中以这种方式处理异常，您必须能够访问 IO monad。处理异常最常用的方法是使用`catch`函数。当您调用可能抛出异常的操作时，您包含了一个“处理程序”函数。该函数将把异常作为一个参数，并处理该情况。如果我们想用 Haskell 编写上面的例子，我们应该首先定义我们的异常类型。我们只需要派生`Show`来为`Exception`类型类派生一个实例:

```
import Control.Exception (Exception)data MyException = NegativeSquareRootException
  deriving (Show)instance Exception MyException
```

现在我们可以编写一个纯粹的函数，在适当的情况下抛出这个异常。

```
import Control.Exception (Exception, throw)integerSquareRoot :: Int -> Int
integerSquareRoot x
  | x < 0 = throw NegativeSquareRootException
  | otherwise = undefined
```

虽然我们可以从纯代码中抛出异常，但我们需要在`IO` monad 中捕捉它。我们将使用`catch`函数来实现这一点。我们将使用一个处理函数，它将只捕捉我们预期的特定错误。它会将错误作为消息打印出来，然后返回一个空值。

```
import Control.Exception (Exception, throw, catch)…
mathFunction :: Int -> IO Int
mathFunction input = do
  catch (return $ integerSquareRoot input) handler
  where
    handler :: MyException -> IO Int
    handler NegativeSquareRootException = 
      print "Can't call square root on a negative number!" >> return (-1)
```

# MonadThrow

我们也可以把这个过程推广到不同的单子上。typeclass 允许我们为不同的单子指定不同的异常行为。例如，`Maybe`通过使用`Nothing`抛出异常。`Either`使用`Left`，`IO`将使用`throwIO`。当我们在一个普通的`MonadThrow`函数中时，我们用`throwM`抛出异常。

```
callWithMaybe :: Maybe Int
callWithMaybe = integerSquareRoot (-5) -- Gives us `Nothing`callWithEither :: Either SomeException Int
callWithEither = integerSquareRoot (-5) -- Gives us `Left NegativeSquareRootException`callWithIO :: IO Int
callWithIO = integerSquareRoot (-5) -- Throws an error as normalintegerSquareRoot :: (MonadThrow m) => Int -> m Int
integerSquareRoot x
  | x < 0 = throwM NegativeSquareRootException
  | otherwise = ...
```

关于额外的抽象层是否有帮助还有一些争论。有一个强有力的例子说明，如果你要使用异常控制流，你无论如何都应该使用`IO`。但是使用`MonadThrow`可以让你的代码更具可扩展性。您的函数可能在代码库的更多区域可用。在这个话题上我不是太固执己见(至少现在还不是)。但是在 Haskell 社区中肯定有一些强烈的意见。

# 摘要

错误处理是棘手的事情。许多常见的错误处理编程模式写起来很烦人。幸运的是，Haskell 有几种不同的方法可以做到这一点。在 Haskell 中，您可以使用类似于`Maybe`和`Either`的简单机制来表达错误。它们的一元行为给了你高度的可组合性。您还可以像在其他语言中一样抛出和捕捉异常。但是 Haskell 有一些更通用的方法来做到这一点。这允许您不知道代码中的函数如何处理错误。

初次接触 Haskell？惊叹于它的牛逼，想试试？下载我们的[入门清单](https://www.mmhaskell.com/checklist)！它有一些很棒的工具和指导，可以让你在电脑上安装 Haskell 并开始使用。

你试过 Haskell 但是想要更多的练习吗？查看我们的[递归练习册](https://www.mmhaskell.com/workbook)，获取一些精彩内容和 10 个练习题！

请继续关注周一早上哈斯克尔的博客！