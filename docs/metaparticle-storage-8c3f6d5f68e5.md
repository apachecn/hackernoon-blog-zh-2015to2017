# 超粒子/存储

> 原文：<https://medium.com/hackernoon/metaparticle-storage-8c3f6d5f68e5>

# 分布式节点应用程序的简单持久性

我对容器化应用程序和容器编排的主流化非常兴奋。老实说，我对将分布式系统民主化以接触新的开发人员受众更感兴趣。我已经谈论这个话题[好几次](https://www.linux.com/news/learn/kubernetes/democratizing-distributed-systems-metaparticle) [次](http://shop.oreilly.com/product/0636920072768.do)，但是今天我很兴奋地讨论[超粒子/存储](https://github.com/metaparticle-io/storage)一个用于*隐式*持久性的新库 [NodeJS](https://hackernoon.com/tagged/nodejs) 。

元粒子/存储通过使持久性隐式化和自动化，使持久性变得简单。您无需显式调用存储基础架构，只需将变量赋值给本地变量，库就会负责底层存储管理。Metaparticle/Storage 不再担心并行、复制的服务器和数据竞争，而是自动处理冲突检测、回滚和解决。

与其深入探究这个库的细节，我想我会带你了解它存在的原因和它试图解决的问题。

# 一个例子:请求计数服务器

在整个讨论中，我们将关注一个简单的服务器，它计算并报告它接收的请求数量。这种服务器的最简单版本可以用几行 Javascript 代码编写:

```
// Simple HTTP Server example, keeps track of the number 
// of requests and reports back over HTTP
var http = require('http');var count = 0;
var server = http.createServer((request, response) => {
    count++;
    var suffix = (count == 1 ? ' request.' : ' requests.');
    response.end('There have been ' + count + suffix);
});server.listen(8090, (err) => { 
    if (err) {
        console.log('error starting server', err)
    }
    console.log('server is listening on http://localhost:8090')
});
```

这个服务器确实非常简单，它跟踪服务的请求数量，直到服务器崩溃或需要重启。因为计数只是存储在内存中，所以每当进程终止时，计数就会丢失。为了保护它，我们需要某种坚持。

# 用 Redis 添加持久性

为了解决这个问题，我们将把 Redis 键值存储集成到我们的应用程序中。Redis 没什么神奇的，其他存储层也同样有用，代码看起来也差不多。

```
// Simple HTTP Server example, keeps track of the number 
// of requests and reports back over HTTP
var http = require('http');
var redis = require('node-redis-client');var count = 0;var host = process.env['REDIS_HOST'];
var opts = {
    host: host
};
var client = new redis(opts);
client.on('connect', function () {
    log.info('connected');
});var server = http.createServer((request, response) => {
    client.call('GET', 'count', function (err, res) {
        if (err) {
            console.log(err);
            return;
        }
        var count = 0;
        if (res != null) {
            count = parseInt(res);
        }
        count++;
        var suffix = (count == 1 ? ' request.' :  ' requests.');        
        client.call('SET', 'count', '' + count, function (err) {
            if (err) {
                console.log(err);
            }
            response.end('There have been ' + count + suffix);            
        });
    }); 
});server.listen(8090, (err) => {  
  if (err) {
    console.log('error starting server', err)
  } console.log('server is listening on [http://localhost:8090](http://localhost:8090`)');
});
```

将持久性添加到我们的服务器中需要注意几件事情:

首先，代码的大小增长了近两倍。

其次，更令人担忧的是，持久存储的引入意味着我们的代码现在包含了对`SET`和`GET`的显式函数调用，不再仅仅是使用标准语言(例如`count = count + 1`)隐式地操作数据。这意味着持久代码看起来不同于“普通”代码，这打破了流程，并给那些刚刚开始学习编码的人带来了障碍。

第三，这些显式调用的异步特性不仅使代码更长，而且也更难理解。同样，这也是开发人员成为成功的分布式系统工程师的障碍。

# 分布式复制服务器的问题

不幸的是，即使处理了持久性，我们的应用程序仍然不能安全地扩展到多个容器。要理解为什么会出现这种情况，请考虑在读取一个值和随后写入该值之间存在竞争。考虑一下，当我们的应用程序的两个不同实例都读取相同的值，将它递增 1，然后都将(相同的)新值写回持久层时会发生什么。我们将为两个用户请求提供服务，但计数只会增加 1。其中一个请求将会丢失。这是一个典型的[读-更新-写](https://en.wikipedia.org/wiki/Read-modify-write)竞赛的例子。

当然，我们可以简单地通过向我们的简单服务器添加额外的代码来定义一个连接服务器的数据读写的原子事务，从而解决这个问题。但是同样，这个事务增加了代码的复杂性，减少了能够成功构建这样一个系统的开发人员的数量。(请记住，顶部的目标是扩大成功应用程序开发人员的范围并使其民主化)。

# 使用超粒子/存储

而不是考虑完整的、原子的、多容器的安全服务器看起来像什么。相反，让我们考虑当使用元粒子/存储实现时，这个示例看起来像什么:

```
// Simple HTTP Server example, keeps track of the number of 
// requests and reports back over HTTP
var http = require('http');
var mp = require('[@metaparticle/storage](http://twitter.com/metaparticle/storage)');mp.setStorage('redis');var server = http.createServer((request, response) => {
    mp.scoped('global', (scope) => {
        if (!scope.count) {
            scope.count = 0;
        }
        scope.count++;
        return scope.count;
    }).then((count) => {
        var suffix = (count == 1 ? ' request.' :  ' requests.');
        response.end("There have been " + count + suffix);
    });
});server.listen(8090, (err) => {  
  if (err) {
    console.log('error starting server', err)
  } console.log('server is listening on [http://localhost:8090](http://localhost:8090`)');
}
```

您可以注意到，这段代码比上面的显式持久性示例要短。由于它是隐式的，而不是显式的持久化，所以读(和写)起来也更容易。然而，上面的代码既使用 Redis 实现持久性，又确保对存储的多个并发读写不会损坏数据。那么，到底发生了什么呢？

# 实施细节

事实证明，Javascript 有一些特殊的功能。其中最酷的是创建阴影或代理对象的能力。这些代理对象看起来像真正的 Javascript 对象，但是它们通过预定义的代理方法代理所有 get/set 操作。当您使用`metaparticle.scoped(scope, fn)`创建一个新的数据范围时，它会返回一个代理对象，拦截所有读写调用。这些操作构成了系统将应用或回滚的事务的基础。元粒子/存储库观察所有这些设置新值的调用，并自动将新数据保存到持久层。这使得像`i = i + 1`这样的隐式持久性在作用域操作结束时被拦截。对代理对象的更改会保存到存储中。此外，因为库控制对存储的访问，所以很容易多次检测、回滚和重新应用相同的代码，以确保线程安全的并发变量更新。

# 没有免费午餐！

当然，就像所有的东西一样，没有免费的午餐。为了使你的代码可行，在`scoped`块中的函数必须是幂等的。也就是说，它可以反复调用，没有额外的副作用。

# 摘要

好了，是时候总结一下了，这篇文章越来越长了，我想你已经发现了有趣的地方。这实际上只是旅程的开始，如果您有兴趣帮助元粒子/存储，代码就在今天的 [github](https://github.com/metaparticle-io/storage) 上。请参加，提出问题或以其他方式与我联系。

最好的！！

布兰登（男子名）