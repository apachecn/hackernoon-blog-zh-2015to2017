# 用 Elm 替换 angular 1 路由器—第 1 部分

> 原文：<https://medium.com/hackernoon/replacing-the-angular-1-router-with-elm-d71753e74e32>

如果您正试图从 angular 1 迁移出去(并且您对 angular 2 不感兴趣),似乎您有两个选择:您可以尝试替换单个组件并从内到外吃掉 angular，或者您可以通过首先替换路由器来尝试从外到内吃掉它。

我对尝试从 angular 1 路由器下面出来很感兴趣，所以我真的想选择后者。这使我开始把 angular 1 仅仅作为一种呈现组件的方式，而不是一个负责所有事情的框架。

我最感兴趣的技术是 Elm，我受到了 Richard Feldman 关于 Elm 中 Web 组件的演讲的启发，在我看来，我们应该能够用 angular 1 组件/指令实现类似的东西。

使用 Elm 的 Html 中的“node”函数来呈现角度指令是很简单的。属性模块；问题是它不会做任何事情，除非 angular 框架知道它已经被添加到 DOM 中，并有机会编译它。

例如，假设我有以下角度分量:

```
angular.module('MyApp', [])
    .component('pageOne', {
    template: '<div>Page One</div>',
    controller: function PageOneController () {
        console.log('we are in page one');
    }
});
```

我可以这样从 Elm 渲染这个:

```
node "page-one" [] []
```

但这对我没有好处，因为 angular 对此一无所知。我们需要告诉 angular 这件事。

当然，Elm 正在发出虚拟 DOM，我们没有明显的方法知道真实 DOM 何时被更新。

# 突变观察者

因此，使用客户端路由解决方案，当路由改变时，我们可以有效地在单个根节点内外交换内容。我们可以使用一个变异观察器来监控根节点，然后在发生变化时指示 angular 编译内容。

大概是这样的:

```
var root = document.getElementById('root');
var observer = new MutationObserver(triggerDigest);
observer.observe(root, { childList: true, subtree: true });function triggerDigest() {
    var $body = angular.element(document.body);            
    var $rootScope = $body.injector().get('$rootScope');  
    var $compile = $body.injector().get('$compile');
    $rootScope.$apply(function() {
        $compile($body)($rootScope);
    });
}
```

因此，这里我们获得了一个对根元素的引用，我们将在其中嵌入 Elm 应用程序。我们创建了一个突变观察器，它将调用一个 triggerDigest 函数。该函数获取对 angular 的$rootScope 及其$compile 服务的引用。然后，它根据根范围编译整个树。

# 无限循环

但是我们有一个问题。目前，我们正在监视整个子树，编译 angular 指令将导致更多的变异，并触发另一次编译。这将导致编译和变异的无限循环，并杀死页面。

一个解决方案是让 Elm 告诉我们什么时候应该开始观察，然后在我们完成编译后就停止观察。这可以使用端口来完成。我们可以像这样在 Elm 中创建一个出站端口:

```
port watchDom : String -> Cmd msg
```

然后，当 url 发生变化时，我们需要向该端口发送一条消息:

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        UrlChange location ->
            ( { model | route = Url.parsePath route location }
            , **(watchDom "")**
            )
```

然后，我们可以如下修改我们的 javascript:

```
var root = document.getElementById('root');
**var app = Elm.Main.embed(root);**
var observer = new MutationObserver(triggerDigest);**app.ports.watchDom.subscribe(function(msg) {
    observer.observe(root, { childList: true, subtree: true });
});**function triggerDigest() {
    var $body = angular.element(document.body);            
    var $rootScope = $body.injector().get('$rootScope');  
    var $compile = $body.injector().get('$compile');
    $rootScope.$apply(function() {
        $compile($body)($rootScope);
    });
    **observer.disconnect();**
}
```

这将打破我们的循环，给我们一个可行的解决方案的开端。不知道它的效率有多高，但至少看起来是有效的。

以这种方式使用端口的一个警告是，这意味着 0.18 调试器将无法正常工作。通过包装“history.pushState”函数并跟踪“popstate”事件，可以避免使用端口。端口的一个优点是，它允许我们提供一些关于我们要导航到的路线的元数据，例如，它是否包含需要编译的内容。

# 还有一个问题

在第二部分中，我将探索如何处理内部角度链接。

这个概念证明的完整源代码可以在[这里](https://github.com/travelrepublic/elm-angular-router)找到。