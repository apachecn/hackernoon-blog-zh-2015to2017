# 准备好无限战争了吗？这是一个简单的使用 ReactJS 的倒计时器！

> 原文：<https://medium.com/hackernoon/ready-for-infinity-war-heres-a-simple-countdown-timer-using-reactjs-2751eae37c0e>

![](img/34f56db36379fe529b11af27e69e6dac.png)

Source: [https://velopert.com/wp-content/uploads/2016/03/react.png](https://velopert.com/wp-content/uploads/2016/03/react.png)

我是漫威的超级粉丝。自从 2008 年《钢铁侠》上映以来，我一直在追踪导致与灭霸大战的故事线索。但同时又喜欢[编程](https://hackernoon.com/tagged/programming)。最近我一直在学习[反应堆](https://hackernoon.com/tagged/reactjs)的一点一滴，我对自己说，“为什么不从我学到的这些随机的技巧中做点有用的东西呢！”看这里。如果字体看起来像 TNR，等待一会儿，刷新页面。下载复仇者联盟字体需要一段时间。

这是我在早期学习阶段制作的首批应用之一。从那以后，我学到了更多。我计划在将来分享我的其他作品来记录我的学习进展。令人惊讶的是，我们没有利用多少本可以用来学习和建设的时间。我度过的间隔年让我意识到。

我对“边做边学”的信念源于建立确定性的想法。起初，我不知道我之前摆弄 ReactJS 到底是为了什么。有大量的试验和错误，就像为初学者建立一个该死的项目！之后，我继续四处查看，确保我从代码中至少理解了一两件我知道对输出有贡献的事情。我仍然在这个过程中(老实说，我不认为你真的会离开这个过程)，我会继续走这条路，因为它帮助我填补空白，同时，建立在以前的知识上，最终将达到更大的项目。

做这件事真的很简单，因为我只需要一个简单的命令，比如 create-react-app，就可以设置好手头的一切。让生活变得比从 Babel 到 Webpack 都要简单得多。程序员是出了名的懒，所以像这样的捷径总是受到欢迎和适应。

我在 Python 和 MATLAB 编程方面的经验也有助于从整体上理解 JS。当你开始意识到它们都以这样或那样的方式遵循相同的结构和逻辑时，将技能移植到其他语言是非常容易的。ReactJS 相当大，更不用说 JS 了，所以我要说还有很长的路要走。

以下是我清理后 html 文件的内容:

```
<!DOCTYPE html>
<html>
<head>
     <meta charset="utf-8">
     <title>ReactJS Countdown Timer</title>
</head><body>
     <noscript>
          You need to enable JavaScript to run this app.
     </noscript>
     <div id="root"></div>
</body>
</html>
```

一切都为你准备好了。下面是主 index.js 文件的样子:

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';ReactDOM.render(<*App* />, document.getElementById('root'));
```

在这里，通过将“App”的内容“附加”到上面 HTML 文件中的根 div 标签来进行呈现。可以把它想象成把一个先前编码的 200 多行的 HTML 文件插入到根 div 标签中。这就是 ReactJS 的工作原理。你用 JavaScript 扩展(JSX)编程，让它看起来像你还在用 HTML 编程，而实际上你用的是 JavaScript。非常棒的东西。

大部分渲染在 App.js 中进行，如下所示。JSX 让你感觉像是在同时写 HTML 和 JS。神奇的东西！

```
import React, { Component } from 'react';
import './App.css';
import Clock from './Clock';*class* App extends *Component* { *constructor*(*props*) {
          super(props);
          this.state = { deadline: 'May, 4, 2018' };
     } render() {
          return (
              <div className="App">
                   <div className="App-title">
                        Avengers: Infinity War
                   </div>
                   <div className="App-date">
                        {this.state.deadline}
                   </div> <*Clock* deadline={ this.state.deadline }/>
              </div>
          );
     }
}
export default App;
```

倒计时定时器的功能放在另一个名为 *Clock.js* 的文件中。大部分的数学和实际渲染，让人们看到实时倒计时都在那里。我还没有直观地理解组件生命周期，所以我还不能简单地解释 *componentWillMount()* 和 *componentDidMount()* 是如何工作的。我知道这行得通，但我就是找不到简单的词来描述*为什么*和*如何*。我不想对我不太了解的事情妄加猜测。告诉过你还有一些差距；我正在努力！

```
import React, { Component } from 'react';
import './App.css';*class* Clock extends *Component* {
     *constructor*(*props*) {
     super(props);
     this.state = {
               days: 0,
               hours: 0,
               minutes: 0,
               seconds: 0,
          };
     } componentWillMount() {
          this.getTimeUntil(this.props.deadline);
     } componentDidMount() {
          setInterval(() *=>* this.getTimeUntil(this.props.deadline), 1000);
     }     leading0(*num*) {
          return num < 10 ? '0' + num : num;
     } getTimeUntil(*deadline*) {
          *const* time = *Date*.parse(deadline) - *Date*.parse(new Date());
          if(time < 0) {
               this.setState({ days: 0, hours: 0, minutes: 0, seconds: 0 });

          } else {
               *const* seconds = Math.floor((time/1000)%60);
               *const* minutes = Math.floor((time/1000/60)%60);
               *const* hours = Math.floor((time/(1000*60*60))%24);
               *const* days = Math.floor(time/(1000*60*60*24));
               this.setState({ days, hours, minutes, seconds });
          }
     } render() {
          return(
               <div>
                    <div className="Clock-days">
                         {this.leading0(this.state.days)} Days
                    </div> <div className="Clock-hours">
                         {this.leading0(this.state.hours)} Hours
                    </div> <div className="Clock-minutes">
                         {this.leading0(this.state.minutes)} Minutes
                    </div> <div className="Clock-seconds">
                         {this.leading0(this.state.seconds)} Seconds
                    </div>
               </div>
          );
     }
}
export default Clock;
```

如果您是 ReactJS 的新手，您可能已经注意到 div 标签有*类名*而不是*类*。记住，我们还在用 JS 编程，单词 *class* 已经被保留了。还有一些像这样的细节。例如，用*常量*或 *let* 代替*变量*。我对这一切还很陌生，所以我还在摸索中。

我把所有的东西都放在了我的 Github 页面上，以防有人想看一看全部内容。

还有很长的路要走！必须不断练习。我是说，我得在网上的某个地方托管我的机器学习后端，对吧？