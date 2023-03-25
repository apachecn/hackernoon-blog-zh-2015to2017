# js-中间件使你的 JS 代码像 ReduxJS 一样可伸缩

> 原文：<https://medium.com/hackernoon/js-middleware-makes-your-js-codes-as-scalable-as-reduxjs-45be85a9196a>

# js-中间件

强大的 Javascript 中间件模式实现，将中间件应用于任何对象。一个无痛的解决方案，使代码像 ReduxJS 和 ExpressJS 一样可伸缩和可维护。

# 链接

*   [项目概述](https://unbug.github.io/js-middleware/)
*   [文档](https://unbug.github.io/js-middleware/docs/html/)
*   [GitHub 回购](https://github.com/unbug/js-middleware)

# 概观

中间件功能是可以访问目标功能及其参数、目标对象以及目标功能循环中的下一个中间件功能的功能。下一个中间件功能通常由名为 next 的变量表示。

中间件功能可以执行以下任务:

*   执行任何代码。
*   对函数的参数进行更改。
*   结束目标函数。
*   调用堆栈中的下一个中间件。

如果当前中间件函数没有结束目标函数循环，它必须调用 next()将控制传递给下一个中间件函数。否则，目标函数将被挂起。

# 开始

1.  窗户。通过在 HTML 中包含`[dist/middleware.min.js](https://github.com/unbug/js-middleware/tree/master/dist)`文件，浏览器可以使用 MiddlewareManager。

```
<script src="middleware.min.js"></script>
```

1.  或者安装软件包

```
npm install --save js-middleware
```

并将其导入您的文件中

```
import {MiddlewareManager} from 'js-middleware';
```

# 习惯

# 基础

我们定义一个人类。

```
// the target object
class Person {
  // the target function
  walk(step) {
    this.step = step;
  }

  speak(word) {
    this.word = word;
  }
 }
```

然后我们定义一个中间件函数来打印日志。

```
// middleware for walk function
 const logger = target => next => (...args) => {
    console.log(`walk start, steps: ${args[0]}.`);
    const result = next(...args);
    console.log(`walk end.`);
    return result;
  }
```

现在，我们将日志函数作为中间件应用于一个人实例。

```
// apply middleware to target object
 const p = new Person();
 const middlewareManager = new MiddlewareManager(p);
 middlewareManager.use('walk', walk);
 p.walk(3);
```

每当一个 Person 实例调用它的 walk 方法时，我们都会看到来自 looger 中间件的日志。

# 中间件对象

我们还可以将中间件对象应用到目标对象。中间件对象是包含与目标对象的函数名相同的函数名的对象。以“_”开头或结尾的函数名将无法应用中间件。

```
const PersonMiddleware {
  walk: target => next => step => {
    console.log(`walk start, steps: step.`);
    const result = next(step);
    console.log(`walk end.`);
    return result;
  },
  speak: target => next => word => {
    word = 'this is a middleware trying to say: ' + word;
    return next(word);
  }
} // apply middleware to target object
 const p = new Person();
 const middlewareManager = new MiddlewareManager(p);
 middlewareManager.use(PersonMiddleware);
 p.walk(3);
 p.speak('hi');
```

# 中间件方法

或者我们可以使用`middlewareMethods`在一个类中为 middleweare 目标定义函数名。

```
class PersonMiddleware {
  constructor() {
    /**
     * Define function names for middleweare target.
     * @type {Array}
     */
    this.middlewareMethods = ['walk', 'speak'];
  }
  log(text) {
    console.log('Middleware log: ' + text);
  }
  walk(target) {
    return next => step => {
      this.log(`walk start, steps: step.`);
      const result = next(step);
      this.log(`walk end.`);
      return result;
    }
  }
  speak(target) {
    return next => word => {
      this.log('this is a middleware tring to say: ' + word);
      return next(word);
    }
  }
} // apply middleware to target object
 const p = new Person();
 const middlewareManager = new MiddlewareManager(p);
 middlewareManager.use(new PersonMiddleware())
 p.walk(3);
 p.speak('hi');
```

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如需了解更多信息，请[阅读我们的关于页面](https://goo.gl/4ofytp)、[喜欢/在脸书](http://bit.ly/HackernoonFB)上留言给我们，或简单地, [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果您喜欢这个故事，我们建议您阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实视为理所当然！