# 用 Elm 替换 angular 1 路由器—第 2 部分

> 原文：<https://medium.com/hackernoon/replacing-the-angular-1-router-with-elm-part-2-bf9c84934abf>

这是[第一部分](/@julianjelfs_61852/replacing-the-angular-1-router-with-elm-d71753e74e32#.2fd4y8xip)的后续帖子。

现在我们成功地渲染了 Elm 路由器下面的角形组件，非常棒。但是有一个问题。如果我们正在尝试迁移使用 angular 路由器的现有 angular 应用程序，那么我们很有可能使用$location 服务来执行程序化的路由更改，例如:

```
$location.path('/pagefour/1234');
```

这不会再起作用了，因为 Elm 在控制。所以我们需要想办法告诉 Elm 改变路线，而不是告诉 angular 改变路线。我们最好不要以任何干扰的方式改变我们的角度代码(因为我们可能有很多角度代码)。

一种方法是修饰$location 服务并覆盖它的默认行为，向一个入站 Elm 端口发送一条消息，以便 Elm 可以接管。大概是这样的:

```
const app = Elm.Main.embed(root);angular.module('MyApp', [])
    .config(function($locationProvider, $provide) {

    $locationProvider.html5Mode(true); $provide.decorator('$location', ['$delegate', 
        function $locationDecorator($delegate) {
            const p = $delegate.path;
            **$delegate.path = function(url) {
                app.ports.newUrl.send(url);
            }**
            return $delegate;
        }]);
});
```

这将拦截所有对$location.path 的调用，并将 url 发送到我们的 newUrl 端口。在 Elm 端，我们需要如下定义该端口:

```
port newUrl : (String -> msg) -> Sub msg
```

然后，我们需要订阅该端口，因此我们的订阅功能变为:

```
subscriptions : Model -> Sub Msg
subscriptions model =
    newUrl NewUrl
```

我们已经在我们的更新功能中处理了 NewUrl 消息，一切似乎都正常。

# 但是仍然有一个问题

锚定标签仍然不起作用。因此，事实证明，修饰$location.path 并不是正确的方法，因为这只是 angular 改变位置的一种方式，我们需要覆盖所有的方式。解决方案是监听 angular 的$locationChangeStart 事件，并在事件发生时将消息发送到我们的 Elm 端口。

```
angular.module('MyApp', []).run(function($rootScope) {
    $rootScope.$on('$locationChangeStart', (e, newUrl) => {
        app.ports.newUrl.send(newUrl);
    });
});
```

这还有最后一个问题。如果路线改变是由 *Elm* 发起的，我们实际上不想听 angular 事件——除非它是由 angular 发起的。因此，我们可以创建一个标志，并在 Elm 触发路由更改时设置它(我们知道何时会发生这种情况，因为我们正在侦听来自 Elm 的出站端口),并稍微修改我们的侦听代码。

```
angular.module('MyApp', []).run(function($rootScope) {
    $rootScope.$on('$locationChangeStart', (e, newUrl) => {
        **if(!listenForRouteChanges) {
            return;
        }**
        app.ports.newUrl.send(newUrl);
    });
});
```

当我们处理完 Elm 发起的路由更改后，我们只需将 listForRouteChanges 标志设置回 true。

# 但是仍然有一个问题

浏览器的历史越来越混乱，更多细节和某种解决方案在[第三部](/@julianjelfs_61852/replacing-the-angular-1-router-with-elm-part-3-d56e40bf251#.k0v54hssi) …

和以前一样，可以在[这里](https://github.com/travelrepublic/elm-angular-router)找到这个概念验证的完整源代码。