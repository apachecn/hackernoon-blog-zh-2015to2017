# 多线程、多重处理和 NodeJS 事件循环

> 原文：<https://medium.com/hackernoon/multithreading-multiprocessing-and-the-nodejs-event-loop-5b2929bd450b>

最近有一位面试官问我关于 NodeJS 中多线程的问题。我声称精通 NodeJS，但面试官和我自己很快意识到有一些基本概念我没有花时间去研究。面试后，我决定阅读 NodeJS 事件循环和多线程。

Node.js 应用程序在单线程上运行。在这个线程中，事件循环监听事件，然后在检测到事件时触发与事件相关的回调函数。通过这个简单的例子，我们可以看出 Node.js 不支持多线程，因为每个应用程序都运行在一个线程上。同样，如果面试官问 Node.js 是否支持多线程，你可以自信地说不。

尽管不支持多线程，但有一种方法可以通过使用进程来利用多核系统的能力。Node.js 有一个名为 **cluster** 的模块，旨在支持多处理替代方案。让我们从 Node.js [docs](https://nodejs.org/api/cluster.html#cluster_cluster) 中查看一些代码。

```
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
  // Fork workers.
  for (var i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  cluster.on('exit', (worker, code, signal) => {
    console.log(`worker ${worker.process.pid} died`);
  });
} else {
  // Workers can share any TCP connection
  // In this case it is an HTTP server
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end('hello world\n');
  }).listen(8000);
}
```

集群模块允许您生成多个子进程，这些子进程可以共享一个公共端口。在此示例中，工作进程正在侦听端口 8000 上的 http 连接。有了子进程，系统可以处理更大的负载。万岁！

我们已经成功实现了一个多核 Node.js 应用，但是每一个设计决策都需要权衡评估。在这种情况下，我们的权衡与多线程和多处理的概念有关。线程共享相同的内存空间，创建新线程不会占用太多系统资源。进程在单独的内存分配中运行，程序的完整副本增加了应用程序的内存开销。在内存不足的情况下，Node.js 可能是应用程序代码最低效的工具。

如果我向我的面试官解释这些概念，我肯定会给他留下深刻印象。不过，没造成什么伤害。最后，这次经历给了我一个学习新东西的机会。

2016 年 4 月 12 日

👋🏽嗨！我是史蒂文·纳特拉。在推特上关注我 [@StevenNatera](https://twitter.com/stevennatera) 。我积极参与了[开源社区](https://github.com/nodox)，特别是 [GatsbyJS](https://www.gatsbyjs.org/contributors/steven-natera) 。我喜欢写代码、初创公司、React 和 Kubernetes。