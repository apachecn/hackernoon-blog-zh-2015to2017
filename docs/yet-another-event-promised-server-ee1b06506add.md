# 另一个事件承诺服务器

> 原文：<https://medium.com/hackernoon/yet-another-event-promised-server-ee1b06506add>

我们很多人在工作或自己的项目中使用 [node.js](https://nodejs.org/) 和 [express](http://expressjs.com/) 。但是现在我们可以使用 async/await 这样有趣的特性。我们可以使用 [koa](http://koajs.com/) 或者为 express 制作错误处理器包装器。但这并不酷。

Express 和 koa 使用内核中的中间件作为主要模式。它是从 Ruby framework [sinatra](http://www.sinatrarb.com/) 复制过来的，并且不是异步的。你可以把它和 loop 进行比较——不是声明性的，也不是异步的。

如果你看看 node.js [server](https://nodejs.org/api/http.html#http_class_http_server) 是如何工作的，你可以看到带有“请求”事件的单个进程。

```
const http = require('http');

const server = http.createServer( (req, res) => {
  ctx.res.statusCode = 200;
  res.end('okay');
});
```

并承诺:

```
const http = require('http');

const server = http.createServer( (req, res) => {
  Promise.resolve({ req, res })
    .then(ctx => {

        ctx.res.statusCode = 200;
        ctx.res.end('okay');

        return ctx;
    });
});
```

因此，如果你使用循环或中间件或任何非异步代码，所有其他请求将等待。node.js 和 javascript 的主要思想是使用非阻塞动作。这就是为什么我们有回调，承诺和异步/等待。

所以经过研究，我决定创建一个新的框架。让我们来认识一下 [YEPS](https://yeps.info/) 。

```
npm i -S yeps
```

## app.js

```
const App = require('yeps');

const app = module.exports = new App();

app.then(async ctx => {
  ctx.res.statusCode = 200;
  ctx.res.end('Ok');
});

app.catch(async (err, ctx) => {
  ctx.res.statusCode = 500;
  ctx.res.end(err.message);
});
```

## bin/www

```
#!/usr/bin/env node

const http = require('http');
const app = require('../app');

http
    .createServer(app.resolve())
    .listen(parseInt(process.env.PORT || '3000', 10));
```

## package.json

```
"scripts": {
  "start": "node bin/www"
}
```

首先，它是 100%基于承诺的框架——内核中没有阻塞动作。

## 像中间件一样的承诺

```
app.then(async ctx => {

  ctx.res.end('test');

  return app.reject(); // the same as Promise.reject()

}).then(async () => {

  // it wont work

}).catch(async () => {

  // it wont work

});
```

你可以自己查看一下[基准测试结果](https://github.com/evheniy/yeps-benchmark)。我用 [koa](https://raw.githubusercontent.com/evheniy/yeps-benchmark/master/reports/koa2_middleware.txt) 和 [express](https://raw.githubusercontent.com/evheniy/yeps-benchmark/master/reports/express_middleware.txt) 测试了 [YEPS](https://raw.githubusercontent.com/evheniy/yeps-benchmark/master/reports/yeps_middleware.txt) 。

YEPS 提供了使用 Promise.all()以并行模式执行模块的可能性。

```
const App = require('yeps');
const app = new App();
const error = require('yeps-error');
const logger = require('yeps-logger');

app.all([
    logger(),
    error()
]);

app.then(async ctx => {
    ctx.res.writeHead(200, {'Content-Type': 'text/plain'});
    ctx.res.end('Ok');
});

app.catch(async (err, ctx) => {
    ctx.res.writeHead(500);
    ctx.res.end(err.message);
});
```

或者

```
app.all([
    logger(),
    error()
]).then(async ctx => {
    ctx.res.writeHead(200, {'Content-Type': 'text/plain'});
    ctx.res.end('Ok');
}).catch(async (err, ctx) => {
    ctx.res.writeHead(500);
    ctx.res.end(err.message);
});
```

如果你需要连接到数据库，做一些其他的东西，比如注册日志系统，错误处理程序等等，这是很有用的

同样的想法我们可以用于路由。你不需要像对于 express 一样，去想哪个路由器最受欢迎，放得更高。我们可以同时开始检查所有路由器，如果找到了就拒绝检查。

```
const Router = require('yeps-router');
const router = new Router();

router.catch({ method: 'GET', url: '/' }).then(async ctx => {
    ctx.res.writeHead(200);
    ctx.res.end('homepage');     
});

router.get('/test').then(async ctx => {
    ctx.res.writeHead(200);
    ctx.res.end('test');     
}).post('/test/:id').then(async ctx => {
    ctx.res.writeHead(200);
    ctx.res.end(ctx.request.params.id);
});

app.then(router.resolve());
```

最后但同样重要的是，如果你已经有了一些 express 中间件，你可以使用 [express wrapper](https://github.com/evheniy/yeps-express-wrapper) ，这很容易移植，只需查看 [YEPS 模块](https://www.npmjs.com/search?q=yeps)。

还有一些例子:

```
const App = require('yeps');
const server = require('yeps-server');
const app = new App();
const error = require('yeps-error');
const Router = require('yeps-router');
const router = new Router();
const wrapper = require('yeps-express-wrapper');
const path = require('path');// express middleware
const bodyParser = require('body-parser');
const favicon = require('serve-favicon');

app.then(wrapper(favicon(path.join(__dirname, 'public', 'favicon.ico'))));app.all([
    error(),
    wrapper(bodyParser.json()),
]);

router.get('/').then(async ctx => {
    console.log(ctx.req.body);
    ctx.res.writeHead(200);
    ctx.res.end('test');
});

app.then(router.resolve());

server.createHttpServer(app);
```

让我们从 node.js 中获得乐趣。