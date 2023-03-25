# 与围棋的互斥体共舞

> 原文：<https://medium.com/hackernoon/dancing-with-go-s-mutexes-92407ae927bf>

*读者级:* ***中级*** *—本文假设您对 Go 及其并发模型有一些基本的了解，并且至少对锁和通道通信形式的数据同步有一点熟悉。*

读者注意:我的一个好朋友启发了这篇文章。当我帮助他解决一些数据竞争问题，并尽我所能给他一些关于数据同步艺术的建议时，我意识到这些建议可以让其他人受益。如果您发现自己继承了一个已经做出某些设计决策的代码库，或者您只是想了解比本文更传统的 Go 同步原语。

当我第一次开始使用 Go 编程语言时，我立刻接受了 Go 的口号“[不要通过共享内存来交流；通过交流分享记忆。](https://blog.golang.org/share-memory-by-communicating)“对我来说，这意味着用“正确的”方式编写所有基于并发的代码，总是使用通道。我的想法是，如果你利用通道，你一定会避免争用、锁定、死锁等陷阱。

![](img/81e9c68744d344d5e16225e8a381c9ce.png)

随着我在 Go 方面的进步，学习编写地道的 Go 并了解最佳实践；我会偶然发现相当大的代码库，在那里你经常会发现人们使用 Go 的 **sync/mutex** 原语、 **sync/atomic** 以及其他一些“低级的”或者“老派的”同步原语。我的第一个想法是，他们做错了，他们显然没有看过 Rob Pike 关于基于通道的并发性的优点的任何演讲，他经常提到东尼·霍尔的[沟通顺序流程](https://en.wikipedia.org/wiki/Communicating_sequential_processes)对设计的影响。

现实是残酷的。Go 社区一遍又一遍地重复着上面的口号，但是**看看许多开源项目，互斥体比比皆是。我与这个难题斗争了一段时间，但最终看到了光明，因为是时候弄脏我的手，把频道推到一边，改变一下了。现在让我们快进到 2015 年，我已经写了大约 2.5 年的 Go，从那时起，我对更传统的基于同步的方法有了一两个顿悟，比如基于互斥锁的方法。说吧，2015 年现在再问我？嘿@deckarep 你还只写只用通道的并发应用吗？今天我回答不，原因如下。**

首先，我们不要忘记务实的重要性。当使用传统的锁定或基于通道的同步来保护共享状态时；让我们从下面这个问题开始:“那么你应该使用哪种方法呢”？原来有一篇[很好的小文章很好地总结了这个答案](https://github.com/golang/go/wiki/MutexOrChannel):

> 使用最有表现力和/或最简单的。
> 
> 一个常见的 Go 新手错误是过度使用频道和 goroutines，仅仅因为它是可能的，和/或因为它很有趣。不要害怕使用[同步。互斥](http://golang.org/pkg/sync/#Mutex)如果这最适合你的问题。Go 是务实的，它让你使用最能解决你的问题的工具，而不是强迫你使用一种风格的代码。

请注意那个例子中的关键词:**富有表现力，简单，过度使用，害怕，务实。**在这里我可以承认几件事:刚拿起围棋的时候我很害怕。我是这门语言的新手，在这么快下结论之前，我需要花时间学习这门语言。参考上面的文章，当我们深入研究一些使用基于互斥锁的最佳实践以及需要注意的事项时，您也会得出自己的结论。上面引用的文章还提供了一些关于互斥和通道的很好的指南。

> 何时使用通道:传递数据所有权、分配工作单元和交流异步结果
> 
> 何时使用互斥:缓存，状态

最终，每个应用程序都是不同的，它可能需要一些实验和错误的开始。对我来说，我遵循上面的指导方针，但让我详细阐述一下。当你需要保护对一个相当简单的数据结构的访问时，比如一个切片，或者一个映射，或者甚至是一些定制的东西，并且如果到所述数据结构的接口是简单的，那么从互斥体开始。此外，将锁定的肮脏细节封装在 API 中总是有帮助的。你的数据结构的最终用户不需要关心你的结构如何进行内部同步。

如果你的基于互斥的同步开始变得难以处理，并且**你正在玩互斥舞**，那么是时候改变策略了。同样，要认识到互斥对于简单场景**保护最小共享状态是有用和直接的。利用他们，但尊重他们，不要让他们失去控制。收回对应用程序逻辑的控制，如果你正在与互斥体战斗，那么请考虑重新考虑你的设计。也许转移到通道更适合您的应用程序逻辑，或者更好的是，**不共享状态周期**。**

> 线程不难，难的是锁。

请理解，我并不提倡在通道上使用互斥。我只是说熟悉这两种同步方法，如果您发现您的基于通道的解决方案似乎过于复杂，那么您还有另一个选择。本文中的主题旨在帮助您编写更好、更易维护和更健壮的代码。我们作为工程师**必须认真对待如何处理共享状态，避免多线程应用中的数据竞争**。Go 使得生成高性能的并发和/或并行应用程序变得非常容易，但是陷阱也是存在的，必须小心构建正确的应用程序。那么让我们进入细节:

**第 1 项**:当声明一个互斥体必须保护对一个或多个字段的访问的结构时，最好将互斥体放在它将保护的字段之上。[下面是 Go 自己的源代码](https://github.com/golang/go/blob/f78a4c84ac8ed44aaf331989aa32e40081fd8f13/misc/cgo/test/cthread.go#L16)中的这个习语的例子。请记住，这纯粹是惯例，并不影响您的应用程序的逻辑。

```
var sum struct {
    sync.Mutex     // <-- this mutex protects
    i int          // <-- this integer underneath
}
```

第 2 项:只在必要的时候持有互斥锁。示例:如果可以避免，不要在基于 IO 的调用中持有互斥体。相反，请确保仅在需要时保护您的资源。例如，如果您在 web 处理程序中做了类似的事情，那么您可以通过序列化对处理程序的访问来有效地消除并发性的影响。

```
// In the code below assume that `mu` solely exists 
// to protect access to the cache variable
// NOTE: Excuse error-handling for brevity// Don't do the following if you can avoid it
func doSomething(){
    mu.Lock()
    item := cache["myKey"]
    http.Get() // Some expensive io call
    mu.Unlock()
}// Instead do the following where possible
func doSomething(){
    mu.Lock()
    item := cache["myKey"]
    mu.Unlock()
    http.Get() // This can take awhile and it's okay!
}
```

**第 3 项**:当一个给定的函数有多个可以返回的位置时，使用 defer 来解锁你的互斥体。这对你来说意味着更少的簿记工作，并且当某人从现在起 3 个月以后来并且增加了提前返回的新情况时，可以减轻死锁。

```
func doSomething(){
    mu.Lock()
    defer mu.Unlock() err := ...
    if err != nil{
        //log error
        return  // <-- your unlock will happen here
    } err = ...
    if err != nil{
        //log error
        return  // <-- or here here
    } return  // <-- and of course here
}
```

然而要小心，在任何情况下都不愿意依赖延期。下面的代码是一个陷阱，当您认为延迟是在块范围而不是函数范围内清除时，就会发生这种情况。

```
func doSomething(){
    for {
        mu.Lock()
        defer mu.Unlock()

        // some interesting code
        // <-- the defer is not executed here as one *may* think
     }
   // <-- it is executed here when the function exits 
}// Therefore the above code will Deadlock!
```

最后，当您有非常简单的函数，没有多条返回路径来挤出一点性能时，可以考虑根本不使用 defer 语句。延迟语句确实有一点经常可以忽略不计的开销。*认为这是一个非常不成熟和不必要的优化*。

**第 4 项**:细粒度锁定可以以更复杂的簿记为代价获得更好的性能，而粗粒度锁定可能性能较低，但簿记更简单。再次强调，设计要务实。如果您发现自己在跳“互斥舞”，那么可能是时候重构代码或者转向基于通道的同步了。

如前所述，如果你能隐藏或封装所使用的同步方法，总是很好的。您的包的用户不需要关心如何保护您的共享状态的复杂性。

在下面的例子中，让我们考虑这样一种情况，我们提供一个 get()方法调用，只有在缓存中至少有一个或多个条目时，才会从缓存中提取。好吧，因为我们需要一个锁来从缓存中取出条目，并获得缓存计数— **这段代码将会死锁**。

```
package mainimport (
 “fmt”
 “sync”
)type DataStore struct {
 sync.Mutex // ← this mutex protects the cache below
 cache map[string]string
}func New() *DataStore{
 return &DataStore{
   cache: make(map[string]string),
 }
}func (ds *DataStore) set(key string, value string) {
 ds.Lock()
 defer ds.Unlock() ds.cache[key] = value
}func (ds *DataStore) get(key string) string {
 ds.Lock()
 defer ds.Unlock() if ds.count() > 0 { <-- count() also takes a lock!
  item := ds.cache[key]
  return item
 }
 return “”
}func (ds *DataStore) count() int {
 ds.Lock()
 defer ds.Unlock()
 return len(ds.cache)
}func main() {
/* Running this below will deadlock because the get() method will       take a lock and will call the count() method which will also take a  lock before the set() method unlocks()
*/ store := New()
 store.set(“Go”, “Lang”)
 result := store.get(“Go”)
 fmt.Println(result)
}
```

处理 **Go 的锁不可重入**这一事实的建议模式如下:

```
package mainimport (
 “fmt”
 “sync”
)type DataStore struct {
 sync.Mutex // ← this mutex protects the cache below
 cache map[string]string
}func New() *DataStore {
 return &DataStore{
 cache: make(map[string]string),
 }
}func (ds *DataStore) set(key string, value string) {
 ds.cache[key] = value
}func (ds *DataStore) get(key string) string {
 if ds.count() > 0 {
 item := ds.cache[key]
 return item
 }
 return “”
}func (ds *DataStore) count() int {
 return len(ds.cache)
}func (ds *DataStore) Set(key string, value string) {
 ds.Lock()
 defer ds.Unlock() ds.set(key, value)
}func (ds *DataStore) Get(key string) string {
 ds.Lock()
 defer ds.Unlock()return ds.get(key)
}func (ds *DataStore) Count() int {
 ds.Lock()
 defer ds.Unlock()
 return ds.count()
}func main() {
 store := New()
 store.Set(“Go”, “Lang”)
 result := store.Get(“Go”)
 fmt.Println(result)
}
```

请注意，在上面的代码中，每个非导出方法都有一个匹配的导出方法。在公共 API 级别操作的导出方法将负责锁定和解锁。然后，它们转发到它们各自的不使用任何锁的非导出方法。这意味着所有导出的代码调用将只锁定一次，以避免重入问题。

**第 6 项:**在上面所有的例子中，我们都使用了基本的**同步。Mutex** lock，它可以简单地:lock()和 Unlock()。同步。互斥锁提供了相同的互斥保证，不管 goroutine 是读还是写数据。也存在**同步。RWMutex** 在读取场景中提供了对锁定语义的更多控制。什么时候你想使用 RWMutex 而不是标准 Mutex？

> 答:当你能绝对保证你的临界区内的代码不会改变共享状态时，使用 RWMutex。

```
// I can safely use a RLock() for count, it does not mutate
func count(){
  rw.RLock() // <-- notice the R in RLock (read-lock)
  defer rw.RUnlock()  <-- notice the R in RUnlock() return len(sharedState)
}// I must use Lock() for set, it mutates the sharedState
func set(key string, value string){
  rw.Lock() // <-- notice we take a 'regular' Lock (write-lock)
  defer rw.Unlock() // <-- notice we Unlock() has no R in it sharedState[key] = value  // <-- mutates the sharedState
}
```

在上面的代码中，我们可以假设“sharedState”变量是某种类型的对象——它可能是一个地图，我们可以在那里查询它的长度。由于上面的函数“count()”遵守“sharedState”变量上没有发生突变的规则，这意味着任意数量的读取器(goroutines)并发调用此方法是安全的。在某些情况下，这可以减少处于阻塞状态的 goroutines 的数量，并有可能保证在大量读取的情况下获得性能提升。但是请记住，当你的代码像“set()”一样改变了共享状态时，你不能使用 rw。RLock()命令，而是 rw。Lock()命令。

**第 7 项:**了解 [Go 的坏屁股和内置种族检测器](https://golang.org/doc/articles/race_detector.html)。比赛检测器甚至在 Go 的标准库中也发现了大量的数据比赛。这就是为什么有种族检测器，也是为什么有很多演讲和文章会比我更好地解释这个工具。

*   如果您还没有在 race 检测器下运行单元/集成测试，作为您的持续构建/交付系统的一部分，现在就设置它。
*   如果没有好的测试来测试应用程序的并发性，那么竞争检测器不会给你带来任何好处。
*   除非真的需要，否则不要在生产中运行它，这会降低性能
*   如果竞争检测器发现一个数据竞争，这是一个真正的数据竞争。
*   如果您不小心的话，竞态条件仍然会显示出基于通道的同步。
*   如果一个 goroutine 以某种方式读写不在临界区内的共享数据，那么世界上所有的锁都救不了你。
*   如果 Go 团队可以在不知不觉中编写数据竞赛，那么你也可以。

总之，我希望这篇文章能提供一些关于处理 Go 互斥体的可靠建议。请玩 Go 的底层同步原语，犯你的错，尊重和理解工具。最重要的是，在你的发展中要务实，并使用合适的工具。不要像我当初一样害怕。如果我总是听那些关于多线程编程和锁定的负面言论，我今天就不会在这个行业中一边使用 Go 这样的优秀语言一边编写优秀的分布式系统。

注意:我喜欢反馈，如果你觉得这很有用，请联系我，发微博或者给我建设性的反馈。

干杯，编码快乐！

@deckarep

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)