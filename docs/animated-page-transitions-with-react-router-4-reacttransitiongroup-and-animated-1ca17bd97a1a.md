# React Router 4 的动画页面过渡，ReactTransitionGroup 和动画

> 原文：<https://medium.com/hackernoon/animated-page-transitions-with-react-router-4-reacttransitiongroup-and-animated-1ca17bd97a1a>

![](img/6ff2a224772a58a0356873ea767a5bac.png)

在本文中，我将向您展示如何使用 ReactTransitionGroup 和动画库中的生命周期方法来制作页面过渡动画。

下面是使用这种模式的一些简单过渡的演示(你也可以在[http://animate . mhaagens . me](http://animate.mhaagens.me)查看现场版本)；

View it live at http://animate.mhaagens.me

好了，快速演示到此结束！
让我们看看如何设置一些简单的路线动画！

**反应设置**

让我们用神奇的 Create React 应用程序安装 React，这是启动和运行 React 项目的简单方法。

如果你还没有安装 Create React App(如果有，跳过这一步)；

```
npm install -g create-react-app
```

然后让我们创建我们的项目；

```
create-react-app animatedroutes && cd animatedroutes
```

然后让我们安装我们的路线和动画包；

```
yarn add react-router-dom animated react-transition-group
```

现在在你最喜欢的编辑器中打开项目并运行；

```
npm start
```

**添加 React 路由器**

打开您的`src/index.js`文件并从 React 添加 BrowserRouter

```
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter } from "react-router-dom";
import App from "./App";
import registerServiceWorker from "./registerServiceWorker";
import "./index.css";ReactDOM.render(
 <BrowserRouter>
   <App />
 </BrowserRouter>,
 document.getElementById("root")
);registerServiceWorker();
```

然后让我们创建两个可以渲染的组件；

第一`src/Home.js`；

```
import React, { Component } from "react";export default class Home extends Component {
 render() {
  return (
   <div className="page">
    <h1>Home</h1>
    <p>Hello from the home page!</p>
   </div>
  )
 }
}
```

然后是`src/Subpage.js`；

```
import React, { Component } from "react";export default class Subpage extends Component {
 render() {
  return (
   <div className="page">
    <h1>Subpage</h1>
    <p>Hello from a sub page!</p>
   </div>
  )
 }
}
```

然后打开`src/App.js`改成这个；

```
import React, { Component } from 'react';
import { Route, Link } from "react-router-dom";import Home from "./Home";
import Subpage from "./Subpage";class App extends Component {
  render() {
    return (
      <div className="App">
        <div className="TopBar">
          <Link to="/">Home</Link>
          <Link to="/subpage">Subpage</Link>
        </div>
          <Route exact path="/" component={Home} />
          <Route exact path="/subpage" component={Subpage} />
      </div>
    );
  }
}export default App;
```

然后去掉`src/App.css`里的所有东西，把下面的粘贴到`src/index.css`；

```
html,
body,
#root {
    height: 100%;
    width: 100%;
}body {
    margin: 0;
    padding: 0;
    font-family: sans-serif;
}.App {
    position: relative;
    display: flex;
    flex-flow: column;
}.TopBar {
    position: fixed;
    top: 0;
    left: 0;
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
    width: 100%;
    height: 62px;
    padding: 0 24px;
}.TopBar a {
    margin-right: 18px;
    text-decoration: none;
}.animated-page-wrapper {
    position: absolute;
    top: 62px;
    left: 0;
    width: 100%;
    height: 100%;
}.page {
    padding: 0 24px;
}
```

好吧。现在，您应该能够在主页和子页面这两条路径之间导航了。

**添加过渡组**

现在我们准备开始动画路线。为了让这个工作正常进行，我们需要改变和添加一些东西；

我们现在将使用 Route render-method 来呈现我们的组件，并将它们包装在一个`<TransitionGroup />`中，而不是用普通的方式呈现我们的路线。

首先像这样在你的`src/App.js`组件中导入 TransitionGroup

```
import TransitionGroup from "react-transition-group/TransitionGroup";
```

然后，我们必须为过渡组添加一个特殊的函数来呈现单个孩子。在`src/App.js`中的`class App extends ...`上方，增加此功能；

```
const firstChild = props => {
  const childrenArray = React.Children.toArray(props.children);
  return childrenArray[0] || null;
};
```

然后去掉你的路由，换成这个；

```
<Route
  exact
  path="/"
  children={({ match, ...rest }) => (
    <TransitionGroup component={firstChild}>
      {match && <Home {...rest} />}
    </TransitionGroup>
)}/>
<Route
   path="/subpage"
   children={({ match, ...rest }) => (
     <TransitionGroup component={firstChild}>
       {match && <Subpage {...rest} />}
     </TransitionGroup>
)}/>
```

您现在可以访问新的生命周期方法，如`componentWillAppear()`、`componentWillEnter()`和`componentWillLeave()`。

让我们用它们来制作一个更高阶的组件，让我们的路线更生动！现在真正的乐趣开始了！

创建我们的动画包装器并用动画制作动画(我能再说一遍动画吗..？)

创建`src/AnimatedWrapper.js`并粘贴在此；

```
import React, { Component } from "react";
import * as Animated from "animated/lib/targets/react-dom";const AnimatedWrapper = WrappedComponent => class AnimatedWrapper
 extends Component {
 constructor(props) {
  super(props);
  this.state = {
   animate: new Animated.Value(0)
  };
 }
 render() {
  return (
   <Animated.div className="animated-page-wrapper">
    <WrappedComponent {...this.props} />
   </Animated.div>
  );
 }
};export default AnimatedWrapper;
```

这里发生了很多事情，所以我来解释一下。

制作一个组件来包装我们的路由组件。它将从 TransitionGroup 接收生命周期方法，我们可以将这些方法用于动画。
我们还使用 Animated 来创建一个值，我们可以用它来动画显示包装我们的子组件的 div 的不同样式属性。

让我们添加一些生命周期方法来激活我们的组件，并添加一个`Animated.template```来渲染和/或插入我们的状态动画值。

将`src/AnimatedWrapper.js`改为这个；

```
import React, { Component } from "react";
import * as Animated from "animated/lib/targets/react-dom";const AnimatedWrapper = WrappedComponent => class AnimatedWrapper
 extends Component {
 constructor(props) {
  super(props);
  this.state = {
   animate: new Animated.Value(0)
  };
 }
 componentWillAppear(cb) {
  Animated.spring(this.state.animate, { toValue: 1 }).start();
  cb();
 }
 componentWillEnter(cb) {
  setTimeout(
   () => Animated.spring(this.state.animate, { toValue: 1 }).start(),
   250
  );
  cb();
 }
 componentWillLeave(cb) {
  Animated.spring(this.state.animate, { toValue: 0 }).start();
  setTimeout(() => cb(), 175);
 }
 render() {
  const style = {
   opacity: Animated.template`${this.state.animate}`,
   transform: Animated.template`
    translate3d(0,${this.state.animate.interpolate({
    inputRange: [0, 1],
    outputRange: ["12px", "0px"]
   })},0)
   `
  };
  return (
   <Animated.div style={style} className="animated-page-wrapper">
    <WrappedComponent {...this.props} />
   </Animated.div>
  );
 }
};
export default AnimatedWrapper;
```

然后我们必须在我们的每个路由组件中导入它，并像这样包装它们；

把`src/Home.js`改成这个；

```
import React, { Component } from "react";
import AnimatedWrapper from "./AnimatedWrapper";class HomeComponent extends Component {
 render() {
  return (
   <div className="page">
    <h1>Home</h1>
    <p>Hello from the home page!</p>
   </div>
  )
 }
}const Home = AnimatedWrapper(HomeComponent);
export default Home;
```

而`src/Subpage.js`到此；

```
import React, { Component } from "react";
import AnimatedWrapper from "./AnimatedWrapper";class SubpageComponent extends Component {
 render() {
  return (
   <div className="page">
    <h1>Subpage</h1>
    <p>Hello from a sub page!</p>
   </div>
  )
 }
}const Subpage = AnimatedWrapper(SubpageComponent);
export default Subpage;
```

就是这样！您的路线现在应该是动画进出！

**进一步学习**

我建议通读动画文档(目前它们相当稀少，我们使用的`Animated.template```功能甚至没有在 Github-issues 之外的文档中记录。
你可以在这里看文档；[http://animatedjs.github.io/interactive-docs/](http://animatedjs.github.io/interactive-docs/)

您也可以在此下载居住在[http://animate.mhaagens.me/](http://animate.mhaagens.me/)
的项目实例；
[https://github.com/mhaagens/animated_routes_react](https://github.com/mhaagens/animated_routes_react)

请在 Medium 或 Twitter 上关注我，了解更多 React 教程；[https://twitter.com/mhaagens](https://twitter.com/mhaagens)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)