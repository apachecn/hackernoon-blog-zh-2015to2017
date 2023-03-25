# 用一个简单的互斥例子简要介绍 AbstractQueuedSynchronizer

> 原文：<https://medium.com/hackernoon/brief-introduction-to-abstractqueuedsynchronizer-by-using-a-simple-mutex-example-2f2bc9aa3c54>

AbstractQueuedSynchronizer 是 Doug Lea 创作的杰作 [java](https://hackernoon.com/tagged/java) 类。正是底层数据结构为 CountDownLatch、Semaphore、ReentrantLock 和 ReadWriteLock 提供了 [acquire](https://hackernoon.com/tagged/acquire) 、tryAcquire、release、tryRelease …接口。所以 AbstractQueuedSynchronizer 基本上是 java.util.concurrent 包的主干。

我做了一个关于 AQS 的简短介绍，试图解释简单的获取和释放是如何工作的，希望你会喜欢。

下面是使用 AQS 实现的互斥体的代码:

在这个视频中，我解释了这个类是如何工作的，以及 AQS 的底层机制。