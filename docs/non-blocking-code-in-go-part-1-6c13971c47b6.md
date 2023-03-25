# Go 中的非阻塞代码(第 1 部分)

> 原文：<https://medium.com/hackernoon/non-blocking-code-in-go-part-1-6c13971c47b6>

最小化互斥量。

这主要是一个检查如何以非阻塞方式编写代码的练习。sync 包中可用的互斥体是保护内存免受不良事件影响的一种完全可以接受的方式——例如，未同步的写入程序。

我还应该指出，“ [***通过交流分享记忆***](https://blog.golang.org/share-memory-by-communicating) ”仍然是你写围棋时应该牢记的口头禅。

有几个可以阻塞的代码示例——通过使用互斥体，或者以缓慢的方式从 chan 中读取。还有…