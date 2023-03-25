# 用 Elm 替换 angular 1 路由器—第 3 部分

> 原文：<https://medium.com/hackernoon/replacing-the-angular-1-router-with-elm-part-3-d56e40bf251>

这是系列文章的第三部分。你也应该读读[第一部分](/@julianjelfs_61852/replacing-the-angular-1-router-with-elm-d71753e74e32#.q661pucta)和[第二部分](/@julianjelfs_61852/replacing-the-angular-1-router-with-elm-part-2-bf9c84934abf#.kdib3ce9i)。

总的来说，我们有一个看起来可行的解决方案，但是我们有两个竞争的运行时试图控制我们的位置，这导致了两个严重的问题。首先，当 *angular* 触发导航时，我们在历史队列中有两个条目，打乱了后退按钮。第二，当 *Elm* 触发导航时，angular 的$location 服务会失去对我们所在页面的跟踪，所以如果你的 angular 代码调用类似$location.path()的东西，它可能会得到错误的答案。我想要一个解决方案，不打破我的角度代码，所以这是不行的。

这就是它变得有点(好吧，很多)古怪的地方:(让我们一个一个地考虑这些问题。当 angular 通过调用$location service 方法或单击 angular 组件中的锚标记来触发导航时，它将更新浏览器 url 和调用 history.pushState(以及其他内容)。我们的问题是，然后我们监听$locationChange 事件，并指示 Elm 更改路由，这将触发另一个对 history.pushState 的调用。

所以其中一个电话必须被阻止。我最初的直觉是防止 angular 插入 pushState。但是如果我们这样做，angular 就会陷入一个无限的摘要循环，因为它会不断地尝试解决$browser 告诉它的内容和$location 告诉它的内容之间的差异。因此，最好让 angular 去做它的事情，毕竟 angular 在这个导航中运行着。在这种情况下，我们必须防止 Elm 随后添加第二个条目。

我们可以通过订阅$ locationChangeSuccess 而不是$locationChangeStart 来做到这一点。当 success 事件触发时，我们知道 angular 已经添加了它的历史条目，并且我们知道 Elm 将触发下一个条目。因此，在这一点上，我们设置了一个标志来指示应该忽略对 pushState 的下一个*调用。*

```
$rootScope.$on('$locationChangeSuccess', function(e, newUrl, oldUrl){
    if(!listenForRouteChanges) {
        return;
    }
    app.ports.newUrl.send(newUrl);
    **//at this point angular will have added a history state
    //already so we should ignore the next one
    ignoreNextPushState = true;**
});
```

为了实际丢弃对 pushState 的下一个调用，我们必须如下截取/修饰历史 api:

```
var ps = window.history.pushState;
window.history.pushState = function() {
    **if(ignoreNextPushState) {
        ignoreNextPushState = false;
        return;
    }**
    ps.apply(window.history, arguments);
}
```

我警告过你这是 hacky！但是它解决了我们的第一个新问题。但是我们仍然面临第二个问题，即当 Elm 启动导航时，angular 会失去对位置的跟踪。恐怕要解决这个问题需要更多的黑客。

当我们的突变观察器告诉我们需要编译根节点时，我们知道我们正处于 Elm 发起的路由更改中，因此 angular 的$location 服务将被置于不同步状态。为了避免这种情况，我发现我们可以使用一个私有的 angular api 来使它与真实的 url 同步，就像这样:

```
$location.$$parseLinkUrl(window.location.href);
```

很明显，我对此感觉不太好，事情变得比最初看起来要复杂一些。但是它工作了，重要的是我没有在 Elm 代码或现有的 angular 代码中做任何奇怪的事情。黑客是自我包含在一个地方，当时间到了，它可以简单地被删除。

# 但是有一个严重的问题！

我们允许 Elm 从 DOM 中删除代表角度分量的元素。这意味着 angular 没有做任何正常的清理工作，这意味着内存泄漏！在第四部中，我会看看我们是否能处理好这个问题。

和以前一样，这个概念证明的完整来源可以在[这里](https://github.com/travelrepublic/elm-angular-router)找到。