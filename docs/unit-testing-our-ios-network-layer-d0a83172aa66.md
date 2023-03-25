# 测试我们的 iOS 网络层

> 原文：<https://medium.com/hackernoon/unit-testing-our-ios-network-layer-d0a83172aa66>

# 为什么？

在过去的一周里，我一直在 WeVat 重构我们的网络层。对于可能由[网络](https://hackernoon.com/tagged/network)引起的错误场景的单元测试覆盖率非常低，所以我认为这是一个撒下更大网的好机会，以确保我们的网络重构不会引入任何麻烦🐞、🕷或🐜南

下面我将简要解释我们正在使用的单元测试套件，然后将转移到我们如何在不编写大量代码的情况下动态测试多个错误场景。

# 方法

**ohhttpsubs**

我在以前的一个项目上使用过 [OHTTPStubs](https://github.com/AliSoftware/OHHTTPStubs) ，它似乎是 iOS 的网络存根库的行业标准。它提供了一套工具来帮助清除任何使用`NSURLConnection, NSURLSession`或`Alamofire`进行的网络调用，因此非常适合我们这里的需求。

我还在库周围创建了一个小包装器，以我们需要的方式向我们提供所有的存根代码。

下面的方法让我们能够用一个 200 和一个定义好的 [JSON](https://hackernoon.com/tagged/json) 响应来连接网络。

```
static func stubNetwork(forService service: Service.Type) { stub(condition: isHost(baseUrl)) { _ in
          guard let path = OHPathForFileInBundle(stubPath(forService: service), Bundle.main) else {
      preconditionFailure(“Stub not found!”)
     } return OHHTTPStubsResponse(
         fileAtPath: path,
         statusCode: 200,
         headers: [ “Content-Type”: “application/json” ]
        )
     }
}
```

我会解释这里发生了什么

1.  我们传入我们想要存根的服务类型。服务是一种协议，我们所有的服务都遵循这种协议。这提供了切换服务的能力。键入并提供每个服务所需的存根数据。
2.  我们给存根一个条件。这将是我们想要存根的主机 url。它将存根任何请求与这个网址。
3.  我们获得本地文件路径，该路径保存响应的 JSON 数据。
4.  返回这个文件，清除所有使用这个 url 的请求，直到使用`OHHTTPStubs.removeAllStubs()`删除这个存根。

**敏捷**

我们选择减少使用 XCTest 提供的一些更冗长的语法和编码结构，并继续使用 [Nimble](https://github.com/Quick/Nimble) 框架。

这让我们从建立期望

```
let expect = expectation(description: “Get Receipts”)
...
expect.fulfill()
...
waitForExpectations(timeout: 2) { (error) in
    XCTAssertTrue(asyncReceipts?.count > 0)
}
```

简单来说:

```
expect(asyncReceipts!.count > 0).toEventually(beTrue())
```

👍🏻👍🏻👍🏻

**测试🙂路径**

这应该在单个测试案例中得到证明。只需设置成功存根，调用服务并断言异步结果已经实现。

```
var asyncReceipts: [Receipts]?
StubHelper.stubNetwork(forService: GetReceiptsService.self)GetReceiptsService.getReceipts(forUser: UUID().uuidString) { (result) in switch result {
     case .success(let receipts):
          asyncReceipts = receipts
     default: break
     }
}expect(asyncReceipts).toEventuallyNot(beNil())
expect(asyncReceipts!.count > 0).toEventually(beTrue())
```

**测试🙁路径**

这是单元测试真正开始证明其价值的地方。

我们可以类似地为网络调用编写一个测试用例，它将为我们想要测试的错误代码设置存根，调用服务，并断言错误是异步设置的。

```
var asyncError: Error?
stubNetworkWithNoConnection()GetReceiptsService.getReceipts(forUser: “”) { (result) in switch result {
    case .failure(let error):
         asyncError = error
    default: break
    }
}expect(asyncError).toEventuallyNot(beNil())
expect(asyncError as! HTTPError).toEventually(equal(HTTPError.noConnection))
```

太酷了。但我听到你说，这似乎可以优化。我们不想每次测试不同的错误时都重复这段代码。

# 让我们协议

因此，考虑一下我们的需求，我们需要在每个服务上测试来自服务器的每个可能的错误响应。我们还需要断言返回的错误是我们所期望的。

**服务可测试协议**

```
protocol ServiceTestable { func testFailures() func setupNetworkFailureTest(withStub stub: (() -> Void), andErrorToAssert errorAssertion: HTTPError)}
```

这里我定义了一个协议，它将为我们如何测试我们的服务类提供一个工具。通过确保我们的服务符合这个协议，我们可以调用`testFailures()`，它将为我们需要的每个场景调用`setupNetworkFailureTest()`函数。然后，我们将存根和我们想要断言的错误注入到测试中。

现在你问的实现在哪里？等一下，我告诉你！

**输入协议扩展**

通过扩展我们的`ServiceTestable`协议，我们可以给我们的测试用例一些默认行为。

```
extension ServiceTestable where Self: WeVatTests {func testFailuresWithStatusCodes() {
         let failures = [ (stub: StubHelper.stubNetworkWithNotFound, error: HTTPError.notFound), (stub: StubHelper.stubNetworkWithForbidden, error: HTTPError.invalidCredentials), (stub: StubHelper.stubNetworkTimeout, error: HTTPError.timeout), (stub: StubHelper.stubNetworkNoConnection, error: HTTPError.noConnection), (stub: StubHelper.stubNetworkWithBadRequest, error: HTTPError.invalidRequest), (stub: StubHelper.stubNetworkWithServerError, error: HTTPError.serverError), (stub: StubHelper.stubNetworkWithUnauthorized, error: HTTPError.invalidCredentials), (stub: StubHelper.stubNetworkWithServerTimeout, error: HTTPError.timeout) ] for failure in failures { setupNetworkFailureTest(withStub: failure.stub, andErrorToAssert: failure.error) } }}
```

这看起来像一大块旧的 o '代码，但它将提供相当大的。我们建立了一个元组数组，每个数组元素包含

1.  对我们要使用的存根的引用。
2.  我们要反对的错误。

现在，符合我们的`ServiceTestable`协议的类将看起来像这样:

```
class GetReceiptsServiceTests: WeVatTests, ServiceTestable { override func tearDown() {
        super.tearDown()
        StubHelper.unStubNetwork()
    } ... func testFailures() {
        testFailuresWithStatusCodes()
    } func setupNetworkFailureTest(withStub stub: (() -> Void), andErrorToAssert errorAssertion: HTTPError) { var asyncError: Error?
        stub() GetReceiptsService.getReceipts(forUser: UUID().uuidString) { (result) in switch result {
              case .failure(let error):
                 asyncError = error
              default: break
             }
         } expect(asyncError).toEventuallyNot(beNil())
         expect(asyncError as! HTTPError).toEventually(equal(errorAssertion))
    }
}
```

我将带你了解现在的情况:

1.  我们现在确保服务测试类符合`ServiceTestable`协议
2.  `testFailures()`在运行我们的单元测试套件时被 XCTest 调用
3.  我们在协议扩展中调用了`testFailuresWithStatusCodes()`。
4.  对于我们想要的每一个错误，这个扩展在调用我们在`setupNetworkFailureTest`中的实现时起作用。

# 包裹

我们现在拥有的是一个可扩展的服务层测试工具。当我们添加另一个服务时，很容易将其与 ServiceTestable 协议联系起来，这将确保所有的错误案例都得到充分的测试。如果我们想添加更多的错误场景，我们可以将它们添加到数组中，并引用它们对应的错误存根。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！