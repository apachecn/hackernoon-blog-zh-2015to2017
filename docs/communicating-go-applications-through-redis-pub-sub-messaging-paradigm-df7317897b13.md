# 通过 Redis 发布/订阅消息传递范例传递 Go 应用程序

> 原文：<https://medium.com/hackernoon/communicating-go-applications-through-redis-pub-sub-messaging-paradigm-df7317897b13>

![](img/468a5101d49b627c7637c16a38f63562.png)

Redis 发布/订阅消息传递模式允许应用程序通过订阅通道来相互对话。订户声明他们对特定频道感兴趣，Redis 将消息推送到所有订阅的客户端，而不知道可能有什么订户(如果有的话)。你可以在维基百科上阅读更多关于发布/订阅模式的内容。

在我们的例子中，我们应用程序的副本作为 docker 群服务运行在不同的机器上，我们需要它们相互通信。我们正在处理**许多**通过 websocket 连接连接到我们系统的用户。这些连接由运行在不同机器或实例上的应用程序的副本来处理。

我们将使用[大猩猩 Websocket](https://github.com/gorilla/websocket) 、 [Redigo](https://github.com/garyburd/redigo) (Redis 客户端)和 [UUID](https://github.com/satori/go.uuid) 来创建唯一 ID。我们将要演示的简单应用程序将在客户机之间转发 JSON 消息。请注意，这是我们在生产中运行的更简单的版本。为了简单和可读性，我将跳过可能的改进。

我们首先声明一个用户结构和一个存储来跟踪连接的用户。

```
type User struct {
    ID   string
    conn *websocket.Conn
}type Store struct {
    Users []*User
    sync.Mutex
}
```

如果你已经注意到，因为我们的应用程序将运行一个 http 服务器，我们用互斥体保护我们的存储。接下来，我们定义要在用户之间交换的消息。

```
type Message struct {
    DeliveryID string `json:"id"`
    Content    string `json:"content"`
}
```

使用池连接进行 Redis 通信可能更好，但为了简单起见，我们将跳过它，并如下初始化 app。

```
var (
    gStore      *Store
    gPubSubConn *redis.PubSubConn
    gRedisConn  = func() (redis.Conn, error) { 
        return redis.Dial("tcp", ":6379") 
    }
)func init() {
    gStore = &Store{
        Users: make([]*User, 0, 1),
    }
}
```

此时，我们可以声明一个函数来创建一个新用户，并通过其唯一的 id 订阅 Redis 发布/订阅通道。

```
func (s *Store) newUser(conn *websocket.Conn) *User {
    u := &User{
        ID:   uuid.NewV4().String(),
        conn: conn,
    } if err := gPubSubConn.Subscribe(u.ID); err != nil {
        panic(err)
    }
    s.Lock()
    defer s.Unlock() s.Users = append(s.Users, u)
    return u
}
```

通常，我们不希望发布/订阅连接错误引起恐慌。出错时，您可以重拨 Redis 或从池中借用另一个连接。所以，我在这里把它作为一个改进点。(*我们在此分配的唯一 ID 示例是 ce 6 df 22 e-b497–4a 71–81 C5–2 da 31 a 5566 e 8。)*

现在，我们已经到了这样一个阶段，我们将收听发布的消息，并将它们发送给用户。

```
func deliverMessages() {
    for {
        switch v := gPubSubConn.Receive().(type) {
        case redis.Message:
            gStore.findAndDeliver(v.Channel, string(v.Data))

        case redis.Subscription:
            log.Printf("subscription message: %s: %s %d\n", v.Channel, v.Kind, v.Count)

        case error:
            log.Println("error pub/sub, delivery has stopped")
            return 
        }
    }
}func (s *Store) findAndDeliver(userID string, content string) {
    m := Message{
        Content: content,
    } for _, u := range s.Users {
        if u.ID == userID {
            if err := u.conn.WriteJSON(m); err != nil {
                log.Printf("error on message delivery e: %s\n", err)
            } else {
                log.Printf("user %s found, message sent\n", userID)
            }
            return
        }
    } log.Printf("user %s not found at our store\n", userID)
}
```

我们将从另一个 goroutine 调用 deliverMessages 函数，以免阻塞我们的应用程序。现在是时候声明我们的主函数了。

```
var serverAddress = ":8080"func main() {
    gRedisConn, err := gRedisConn()
    if err != nil {
        panic(err)
    }
    defer gRedisConn.Close() gPubSubConn = &redis.PubSubConn{Conn: gRedisConn}
    defer gPubSubConn.Close() go deliverMessages() http.HandleFunc("/ws", wsHandler)
    log.Printf("server started at %s\n", serverAddress)

    log.Fatal(http.ListenAndServe(serverAddress, nil))
}
```

我们的负责人。

```
var upgrader = websocket.Upgrader{
    CheckOrigin: func(r *http.Request) bool {
        return true
    },
}func wsHandler(w http.ResponseWriter, r *http.Request) {
    conn, err := upgrader.Upgrade(w, r, nil)
    if err != nil {
        log.Printf("upgrader error %s\n" + err.Error())
        return
    }
    u := gStore.newUser(conn)
    log.Printf("user %s joined\n", u.ID) for {
        var m Message if err := u.conn.ReadJSON(&m); err != nil {
            log.Printf("error on ws. message %s\n", err)
        } if c, err := gRedisConn(); err != nil {
            log.Printf("error on redis conn. %s\n", err)
        } else {
            c.Do("PUBLISH", m.DeliveryID, string(m.Content))
        }
    }
}
```

WebSockets 可以执行跨域通信，并且不受 SOP(同源策略)的限制。默认升级程序检查传入请求的源字段和主机头值，以确认它们在允许请求之前相等。如果您想使用默认的 CheckOrigin 函数，您需要确保您的客户端在其请求中包含 Origin 头。同样，为了便于演示，我们覆盖了该函数，但是出于安全考虑，您不应该这样做。

这是你基本需要的全部。您可以在这里查看 [**代码片段**](https://gist.github.com/manorie/760b5d6837b243e0892f844297893e02) 。

如果你已经到了这个地步，那就来试试吧。我启动了我的服务器，打开了 Chrome 开发者工具。在控制台中执行如下命令。

```
> var con0 = new WebSocket('ws://localhost:8080/ws')
> undefined
> var con1 = new WebSocket('ws://localhost:8080/ws')
> undefined
```

我们的服务器将这些连接记录为，

```
2017/01/21 17:26:46 server started at :80802017/01/21 17:28:24 user 7c27943d-dd98-4bfe-829f-7bd9834f9f63 joined2017/01/21 17:28:24 subscription message: 7c27943d-dd98-4bfe-829f-7bd9834f9f63: subscribe 12017/01/21 17:28:28 user ffbe9040-b424-4bdc-89a9-f8b045c878c6 joined2017/01/21 17:28:28 subscription message: ffbe9040-b424-4bdc-89a9-f8b045c878c6: subscribe 2
```

如您所见，我们的第一个和第二个 websocket 连接创建了两个用户:

用户 1 ID:7c 27943d-dd98–4 bfe-829 f-7bd 9834 f9f 63

用户 2 ID:ffbe 9040-b424–4 BDC-89 a9-f8b 045 c 878 c 6

让我们从用户 2 向用户 1(由第一个 websocket 连接表示)发送消息。

```
> con0.onmessage = function(e) { console.log("connection 0 received message", e.data) }

> var mes = new Object()
> mes.id = "7c27943d-dd98–4bfe-829f-7bd9834f9f63"
> mes.content = "hello"> con1.send(JSON.stringify(mes))**> VM154:1 connection 0 received message {"id":"","content":"hello"}**
```

我们的服务器日志，

```
2017/01/21 17:32:58 user 7c27943d-dd98-4bfe-829f-7bd9834f9f63 found at our store, message sent
```

**结论**

这种实现的好处是，如果您以分布式/集群方式运行您的应用程序，您可以轻松地扩展它。如果你正在使用 Docker Swarm(像我们一样)，Kubernetes 或类似的集群服务，扩展是很容易的，这种模式在像我们这样的情况下工作得很好。

注意
你应该处理 websocket 连接关闭(浏览器关闭等。)和 Redis 连接。这只是一个演示脚本。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)