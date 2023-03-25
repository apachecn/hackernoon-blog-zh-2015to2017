# 带有 PromiseKit 的 Swift 上的 Promise 模式

> 原文：<https://medium.com/hackernoon/promise-pattern-on-swift-with-promisekit-620efad7a5bd>

## 以及为什么应该将异步调用迁移到它们

![](img/71c4ffff8603d753c80943fc14a77cc0.png)

异步 API/服务器调用几乎是移动应用程序的一个主要部分，从卸载硬件工作到设计可靠的网络行为，它有几个优点。

如果你开发 iOS 应用已经有一段时间了，你可能已经知道这方面的惯例:**闭包**

> 闭包可以从定义它们的上下文中获取并存储对任何常量和变量的引用。这就是所谓的封闭那些常量和变量。Swift 为您处理捕获的所有内存[管理](https://hackernoon.com/tagged/management)。
> 
> ~ https://开发者. apple.com

可以理解，这是实现轻度异步通信的首选[设计](https://hackernoon.com/tagged/design)模式。您可以快速地让 API 调用访问您的局部变量，在那里它们可以随时调用/操作它们。

像任何 OOP 模式一样，它们也可能被滥用。以这头野兽为例:

Behold, the Pyramid of D00m

仔细观察，您会发现这段代码只是试图连续执行 3 个异步任务:

1.  为用户上传图像
2.  为用户注册一个帐户
3.  为用户登录令牌

每一个异步调用都带有一个完成块，它处理可以以两种不同方式切换的响应:成功或失败。

现在，如果写这篇文章对你来说还不是一个巨大的痛苦，那么等到你必须保持它的时候。为了处理错误和成功的响应，您必须从 3 个不同的块中检查和编辑您的代码，也就是说，如果您还没有忘记应该编辑哪个块的话。

当然，一定有更好的方法来处理这件事？

# 承诺模式

> 承诺是表示异步任务的对象。传递该对象，并编写干净有序的代码；从一个异步任务到另一个异步任务的逻辑的、简单的、模块化的进展流。
> 
> ~http://promisekit.org/docs

在其他语言中有时称为 ***Futures*** ， ***Delay，*** 或 ***Deferred*** ，它们是专门为处理可能成功也可能失败的异步任务而构建的。

但是与闭包不同，承诺被设计成**传递**而不需要只在一个块中捕获整个上下文及其结果。

该设计的工作原理是，实际上*将*异步函数包装在一个对象周围，并且只通过 ***公开它们*承诺的*最终结果。然后{ }*** 块，然后可以修改它返回另一个承诺或其他对象。

```
intPromise.then { intResult -> Promise<Bool> in
    return boolPromise()
}.then { boolResult -> Promise<String>
    return stringPromise()
}.then { stringResult -> Void
    //finally do something with 
    //your end result here
}.catch { error in
    //oh noes error
}
```

这个例子展示了 3 个异步函数是如何相互级联的，其中它们的所有最终结果都可以使用 **PromiseKit** 干净地处理。

不仅你的错误被处理在一个单独的程序块中，所有进行中的任务被安排在一个更干净的顺序中，避免了长的缩进。

到目前为止一切顺利吗？厉害！

现在让我们看看是否可以将我们最初的“注册”代码转换成类似的代码。

# 整合承诺

```
use_frameworks!
swift_version = "3.0"
pod "PromiseKit", "~> 4.0"
```

让我们从通过 pods 安装库开始，或者对于你喜欢的其他方法，查看他们的自述【https://github.com/mxcl/PromiseKit

在我们之前的用户注册代码中，我们可以看到它使用了 3 个不同的服务器 API 调用，即:

纸上看起来很干净，是吗？让我们看看我们还能如何改进这一点。

## 转换异步调用

我们将从把**闭包**作为参数开始全面修改我们的调用:

```
func asyncCall(parameter: String, completion: (String) -> Void)
```

回到承诺:

```
func asyncCall(parameter: String) -> Promise<String>
```

将使用块创建一个 Promise 对象，具有一个 ***实现*** 和 ***拒绝*** 参数。这两个是简单的函数，它们将接受泛型类型化参数或一个错误作为结果。

您可以在这个块中自由地实现异步调用，然后调用参数来发送结果信号。

理想情况下，重构后的函数如下所示:

a bit longer than our original calls, but take it for now

另外，请注意，我们现在正在模拟一个错误结果。这只是为了演示错误处理。现在，尝试使用新的承诺重新完成整个注册流程:

Look at the Closures. Then look at these Promises. Look at the Closures again. Then look at these Promises.

现在看起来不是更好吗？现在，您可以轻松管理您的呼叫、结果和错误的去向。它的可读性和可维护性很强。

## 包装现有异步调用

如果为每个 API 调用创建新的承诺对您的开发流程来说似乎有点太多，那么还有另一个选择。实现起来有点繁琐，但这意味着需要编写更少的新 API:

```
public func wrap<T>(_ body: (@escaping (T?, Error?) -> Void) throws -> Void) -> Promise<T>
```

**Wrap** 函数可从 PromiseKit 的库中获得，它们根据您的情况提供不同的参数选项。在这种情况下，假设您有一个如下所示的函数:

```
func asyncCall(parameter: String, completion: @escaping (Token?, Error?) -> Void)
```

包装它将简单地看起来像这样:

```
wrap({ asyncCall(parameter: String, completion: $0) })
```

现在，这将返回一个新的承诺，它将复制您的原始结果类型，并将其作为自己的结果类型传递。因为我们(嗯，至少*我*会)正在编写由枚举表示的结果对象:

```
enum Result<T>{case success(result: T)case failure(error: Error)}
```

我们必须编写一个新的包装函数来容纳我们的流:

仔细观察，您会发现这只是将您的闭包块转换成 Promise 对象。

现在再次尝试实现原始的注册流程，但是现在使用包装的闭包:

still looking good!

看来你得多写几个字了，是吧？

在大多数情况下，我更喜欢这样。它使我不必重写或复制我的函数，从而减少可能的错误并提高代码的可维护性。

## 阿拉莫菲尔承诺

如果您使用 Alamofire 进行 REST 调用，那么您应该检查一下他们的 Alamofire 扩展库，您可以通过它

```
pod 'PromiseKit/Alamofire', '~> 4.0'
```

或者在这里查看完整自述:[https://github.com/PromiseKit/Alamofire-](https://github.com/PromiseKit/Alamofire-)

它附带的选项有:

```
public func responseJsonDictionary(options: JSONSerialization.ReadingOptions = .allowFragments) -> Promise<[String: Any]>
```

这使得 Alamofire 的数据请求返回一个 Promise 对象，而不是必须通过一个块来捕获它们。一个例子是这样的登录调用:

This code is copied straight out of one of my projects

这是我使用 ***抛出*** 来处理错误的为数不多的案例之一，而且非常干净利落。

## 下一步是什么？

承诺很有趣！从慢慢地将它们整合到你认为合适的项目中开始。直接从他们的网站上阅读更多关于 PromiseKit 的令人敬畏的用法:[http://promisekit.org/docs/](http://promisekit.org/docs/)

寻找闭包开始变得混乱的地方。尝试包装一些函数。在代码中引入更多的承诺，而不是完成块。

就像尝试任何新的设计模式一样，不要害怕后退一步，重新评估它们是否真正适合你的风格。

祝你好运！

## 快速跟进

如果你想看关于如何管理你的自定义异步调用的更深入的指南，我写了一个简短的指南，所以请随意查看:

[https://hacker noon . com/wrapping-up-API-with-promise kit-d 1302 f 446 ad 6](https://hackernoon.com/wrapping-up-apis-with-promisekit-d1302f446ad6)

# 你大老远跑来了！

> 我希望当你打开这篇文章的时候，你已经得到了你想要的东西。如果是这样，如果你能把这个推荐给你的朋友^^，我将非常感激

如果您还有任何问题/反馈，请随时留言！