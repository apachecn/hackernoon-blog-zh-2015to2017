# 用 Elm 替换 angular 1 路由器—第 4 部分

> 原文：<https://medium.com/hackernoon/replacing-the-angular-1-router-with-elm-part-4-f0ace653d93c>

这是系列文章的第四部分。你也要看[第一部](/@julianjelfs_61852/replacing-the-angular-1-router-with-elm-d71753e74e32#.q661pucta)、[第二部](/@julianjelfs_61852/replacing-the-angular-1-router-with-elm-part-2-bf9c84934abf#.kdib3ce9i)和[第三部](/@julianjelfs_61852/replacing-the-angular-1-router-with-elm-part-3-d56e40bf251#.tp2toln2g)。

所以我们至少还有一个主要问题。当 Elm 作为路由更改的一部分删除组件树的根节点时，我们可能会删除 angular 控制下的一个大的子树，而不通知 angular，也不给它执行正常清理操作的机会。这可能并且确实会导致内存泄漏并最终崩溃。您不会在示例代码中注意到这一点，因为效果可能太小了。

概括地说，当 Elm 改变路由时，我们触发一个摘要循环，如下所示:

```
function triggerDigest() {
    if(!compile) {
        return;
    }
    var $body = angular.element(document.body);            
    var $rootScope = $body.injector().get('$rootScope');  
    var $location = $body.injector().get('$location');  
    var $compile = $body.injector().get('$compile'); $location.$$parseLinkUrl(window.location.href);
    $rootScope.$apply(function() {
        $compile($body)($rootScope);
    });
    observer.disconnect();
    listenForRouteChanges = true;
}
```

为了保持 angular 的$destroy 行为，并希望避免内存泄漏，我们需要做一些不同的事情。我们必须每次从$rootScope 创建一个*新的*作用域，然后根据这个新的作用域编译我们的根节点。然后在下一次迭代中，如果我们创建的范围存在，我们将在用下一个新范围覆盖它之前调用$destroy。这样，angular 将通过作用域层次结构向下级联$destroy 调用，所有需要清理的内容都将被清理。所以上面的代码现在看起来像这样:

```
**let activeScope = null;**function triggerDigest() {
    **if(activeScope) {
        activeScope.$destroy();
    }** if(!compile) {
        return;
    }
    var $body = angular.element(document.body);            
    var $rootScope = $body.injector().get('$rootScope');  
    var $location = $body.injector().get('$location');  
    var $compile = $body.injector().get('$compile'); **activeScope = $rootScope.$new();** $location.$$parseLinkUrl(window.location.href);
    **activeScope.$apply(function() {
        $compile($body)(activeScope);
    });**
    observer.disconnect();
    listenForRouteChanges = true;
}
```

使用 AngularJS chrome 插件，现在很容易看到，正如我们所期望的那样，范围层次结构在每次路由更改时都被修剪了，内存泄漏问题有望消失(或者至少不会比使用常规 angular 时更糟)。

和以前一样，可以在[这里](https://github.com/travelrepublic/elm-angular-router)找到这个概念证明的完整来源。