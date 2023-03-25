# 在单页 Web 应用程序中使用 React 的路由器(基础)

> 原文：<https://medium.com/hackernoon/using-reacts-router-in-single-page-web-apps-basics-79ebcd55eadf>

让我们面对现实吧。有了 spa，未来就在**这里**。

建一个温泉浴场超级酷，超级刺激。为什么这么刺激？我猜这是当你换了一个页面而没有进入一个新的网页时那种强烈的成就感。它给了我们一种高傲的优越感，胜过 90 年代的网络——“我们征服了你，弱者。”或者可能是流动性的问题。这种权力感和权威感是因为知道用户的眼睛会死死地盯着他们的屏幕，没有过去习惯的快速喝咖啡或击鼓的习惯。“你永远是我的”，电影对观众说。

因此，本着这种精神，这里是[反应](https://hackernoon.com/tagged/react)路由器的基础知识。

对于 NPM，在您的 React 项目中，安装`react-router-dom`。

您需要从包中导入`BrowserRouter`组件。

`BrowserRouter`是我们的包装。这将包装所有的路由，让 React 知道这是一个路由器。这里有一个例子:

```
import { BrowserRouter as Router } from "react-router-dom"const App = () => {
  return (
    <Router>
      <div>      
        ...routes
      </div>
    </Router>
  )
}
```

我们需要的下一件东西是`Route`组件(也来自`react-router-dom`)。

`Route`组件是我们每次定义路线时使用的组件。它将包含一个`path`和一个`component`以在 url 路径匹配时呈现。示例:

```
import { BrowserRouter as Router, Route } from "react-router-dom"
import { nav } from "/components/nav"
import { homepage } from "/components/homepage"const App = () => {
  return (
    <Router>
      <div>      
        <Route path="/" component={nav}>
        <Route path="/home" component={homepage}>
      </div>     
    </Router>
  )
}
```

在这个例子中，我们将渲染路径`"/"`上的`nav`组件，并且我们将匹配路径`"/home”`上的`homepage`组件。

然而，有一个陷阱！就像在常规的 Node/Express 应用程序中一样，`"/"`路线也将匹配所有其他路线！因此，我们的导航组件将同时呈现在`"/"`路线和`"/home"`路线上。

当然，在大型应用程序中，您需要一种方法来解决这个问题。有两种方法…

第一个选项是使用`exact`关键字。那么组件将只在 url 路径完全匹配的情况下呈现。示例:

```
import { BrowserRouter as Router, Route } from "react-router-dom"
import { welcome } from "/components/welcome"
import { homepage } from "/components/homepage"const App = () => {
  return (
    <Router>
      <div>     
        <Route exact path="/welcome/home" component={homepage}>      
        <Route exact path="/welcome" component={welcome}>
      </div>    
    </Router>
  )
}
```

第二个选项是使用库中另一个名为`Switch`的组件。就像普通的 [JavaScript](https://hackernoon.com/tagged/javascript) switch 案例一样，这将只匹配一个路由(无论哪一个先匹配)。示例:

```
import { 
  BrowserRouter as Router, 
  Route, 
  Switch } from "react-router-dom"
import { welcome } from "/components/welcome"
import { homepage } from "/components/homepage"const App = () => {
  return (
    <Router>
      <Switch>
        <Route path="/welcome/home" component={homepage}>
        <Route path="/welcome" component={welcome}>
      </Switch>
    </Router>
  )
}
```

注意，即使 url 路径`welcome/home`应该匹配两个路由，我们也将只呈现第一个匹配的路由，从而呈现主页组件。很酷吧。

这是路由器的基本设置和使用；然而，这个库还有更多的内容需要探索，所以请查看所有好东西的文档！

如果你刚刚开始水疗，那么我希望这是有帮助的，祝你在冒险中好运！