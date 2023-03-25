# MVP——如何只用一种方法在视图层呈现不同的错误？

> 原文：<https://medium.com/hackernoon/mvp-how-to-present-different-errors-on-view-layer-using-only-one-method-743d6d4b739f>

![](img/bc39e50023f79305e178f4a4ac9050db.png)

使用 **MVP** 我们总是忙于*接口*来将不同的消息从**呈现者**层传输到**视图**层，然后[将它们呈现给](https://hackernoon.com/tagged/presenting)用户，但是当涉及到错误处理时，所有这些方法都是必需的并且很可能被解雇或者甚至是可预测的吗？

# 到底是什么问题？

*   *showerronoffline()*
*   *showErrorOnServiceNotResponding()*
*   *showerroronunabletopresult()*
*   *showerronnoresponse()*
*   *我们知道可能还有更多……*

![](img/f5717cf647338633ae72f588b72f44e1.png)

我们想向用户显示错误，所以我们为每个错误添加了一些*方法*到他们的**视图**的*接口*中，并由**展示者**调用它们，但是有多少错误将被处理呢？当我们要在一个所有视图都扩展的基础视图接口中一般化一些通用方法时，这种困境也会显现出来。

最近我有了一个新想法，值得与社区分享，并获得一些反馈…

# 空谈不值钱；给我看看代码

我过去常常将我的接口一般化，并从一些基本接口扩展它们，这些基本接口包含每个人需要的任何东西:

```
public interface **BaseView** {

    void showMessage(String message);

    void showOfflineMessage(boolean isCritical);

    void **showErrorMessage**(Throwable throwable);

    void showProgress();

    void hideProgress();}
```

并且在我的孩子**视图**界面*中*我把它扩展成:

```
public interface **TopUpView** extends **BaseView** {

    void showBankGateway(String url); void **showBankGatewayError**(); void setVoucherResult(int amount, String voucher); void **showVoucherError**();}
```

但是等一下！在所有*子接口*之间共享一个方法来处理错误不是更方便吗？

在这个例子中，我删除了 TopUpView 中的所有错误方法，并尝试使用 BaseView 中的方法(即 *showErrorMessage* )

首先，我必须提到，通常这些错误发生在 **RxJava** 的 *onError* 实现中，这通常来自于使用**改型**的 API 调用:

```
if (isConnected) {
    subscription = interactor.applyVoucher(voucher, amount)
            .subscribe(calculateResponse ->
            {
                if (null != view)
                    view.hideProgress(); /* ... */
            },
            throwable -> {
                if (null != view) {
                    view.hideProgress();

                    if (isConnected)
                        **view.showVoucherError();**
                    else
                        view.showOfflineMessage(false);
                }
            };
} else {
    if (null != view)
        view.showOfflineMessage(false);
}
```

正如我提到的，有一个通用的 *showErrorMessage()* 方法从未被调用过，忽略了坚实的原则:

> [接口分离原则](https://en.wikipedia.org/wiki/Interface_segregation_principle):“许多特定于客户端的接口比一个通用接口要好。”

![](img/159e65376a3d23f77941f9b580adb448.png)

# ，这里有一个更好的解决方案…

我是 Java 的粉丝，既然 Java 中的错误是使用异常传播的，为什么不在 MVP 层通信中使用它们呢？

让我们为可能发生的错误创建一些自定义异常类:

```
public class **VoucherException** extends **Exception** {

    public **VoucherException**(Throwable throwable) {
        super(throwable);
    }
}
```

请记住，如果是这样的话，您可以封装更多的字段，然后将它们传递给视图:

```
throwable -> {
    if (null != view) {
        view.hideProgress();

    if (isConnected)
        view.showErrorMessage(new **VoucherException**(throwable));
    else
        view.showOfflineMessage(false);
    }
};
```

这就是 **BaseView 的** *showErrorMessage()* 处理错误发生的方式，其他视图界面变得更加整洁:

```
public interface **TopUpView** extends **BaseView** {

    void showBankGateway(String url); void setVoucherResult(int amount, String voucher);}
```

，我们根据收到的异常在我们的**活动**或**片段**中显示错误:

```
@Override
public void showErrorMessage(Throwable throwable) {
    Timber.*e*(throwable, "Error!");

    if (throwable *instanceof* **VoucherException**)
        showToastMessage(getString(R.string.*error_voucher*));
    else if (throwable *instanceof* **TopUpException**)
        showToastMessage(getString(R.string.*error_application*));
}
```

最后但同样重要的是，我们不需要在 **Presenter** 层中硬编码任何错误消息！！！这就是**视图**层如何向用户显示来自资源(R 文件)的相关错误消息！

![](img/87d01191b3fb5c97b534818d07949488.png)

# TL；速度三角形定位法(dead reckoning)

我在我的 **BaseView** *接口*中使用一个 **showErrorMessage** 方法使用异常来处理错误，并试图通过删除重复的错误方法来清理我的代码。

# 更新:

根据这篇文章的一些回复和更多关于 **MVP 的研究，**这个解决方案很好，但不是最佳实践，你必须在**视图**层使用一个单独的方法来处理**演示者**内部发生的所有异常。

![](img/786de5ac882873d02db4e4bfff1ad7e4.png)

感谢你阅读这篇文章。我总是忙于改进和清理我的代码，所以请留下评论，如果你对这个解决方案没有意见，并将在你的项目中使用它？如果不是，为什么？

如果你觉得这篇文章有用就分享吧，关注我更多文章 [Mohsen Mirhoseini Argi](https://medium.com/u/6a4ed0c4dd2c?source=post_page-----743d6d4b739f--------------------------------) 。

 [## Mohsen Mirhoseini -高级 Android 开发人员

### 我是一名资深 Android 开发人员，在计算机软件工程领域有大约九年的经验。我有…

mirhoseini.info](http://mirhoseini.info) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)