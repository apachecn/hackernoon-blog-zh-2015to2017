# 正在运行的套接字

> 原文：<https://medium.com/hackernoon/this-post-series-aims-to-clarify-the-different-operations-you-could-do-with-a-network-socket-and-65dbe59294f8>

本系列文章旨在阐明您可以使用网络套接字进行的不同操作，以及在讨论这些操作时要使用的动词。

那么什么是插座，我为什么要关心我能用它做的事情呢？来自维基百科:

> 网络套接字是计算机网络中连接的端点。它是一个程序可以传递给网络应用程序[编程](https://hackernoon.com/tagged/proghramming)接口(API)的句柄(抽象引用)，以使用连接来接收和发送数据。套接字通常在内部表示为整数。

因此，如果您的应用程序以某种方式使用套接字，并且很可能是这样，您应该熟悉套接字上的操作。让我们从创建一个套接字开始。

## 创建套接字

创建一个套接字是通过[套接字](https://linux.die.net/man/2/socket)系统调用完成的。要创建一个套接字，我们需要指定 3 样东西——这是套接字方法的签名:

```
**#include <sys/types.h>**
**#include <sys/socket.h>****int socket(int** *domain***, int** *type***, int** *protocol***);**
```

那么，什么是域、类型和协议呢？

域指定将用于通信的协议族。协议族是[网络](https://hackernoon.com/tagged/network)接口配置中的一组逻辑属性。协议族包括组成协议组的所有协议。最流行的是 IPv4、IPv6 和 UNIX。

该类型描述了协议的语义，这些协议是由前面的参数指定的协议族的一部分，例如，我想要一个具有传递保证的面向双向连接的字节流，或者我只想发送数据报(固定最大长度的无连接、不可靠的消息)。

好了，我们已经指定了协议族，以及我们想从中获得什么。最后一个参数指定了该系列的具体协议实现。一般情况下，只有一个协议满足(family，type)对，所以省略此参数(`0`被传递)，但如果存在多个实现，则必须相应地设置此参数。

创建套接字后，我们可以连接到远程套接字，或者监听传入的连接。先说后面的。

## 将地址绑定到套接字

为了监听传入的连接，或者只是接收数据报，我们首先需要为套接字指定一个地址(就像给套接字分配一个名称一样)。这个操作被称为绑定——我们[将一个地址(名称)绑定到套接字。](https://linux.die.net/man/2/bind)

```
**int** **bind(int** sockfd, **const struct sockaddr ***addr,
                **socklen_t** addrlen**);**
```

这个调用接受套接字描述符、应该使用的地址和地址类型的字节大小。为`addr`参数传递的实际结构将取决于地址族(我们之前讨论过协议族，还记得吗？).`struct sockaddr`结构的唯一目的是强制转换在`addr`中传递的结构指针，以避免编译器警告。让我们看一个将 127.0.0.1:8000 IPv4/TCP 地址绑定到套接字的示例。(为简单起见，省略了错误处理)

```
int sockfd;
struct sockaddr_in serv_addr;sockfd = socket(PF_INET, SOCK_STREAM, 0);memset(&serv_addr, 0, sizeof(serv_addr));serv_addr.sin_family = AF_INET;
serv_addr.sin_addr.s_addr = INADDR_LOOPBACK;
serv_addr.sin_port = htons(8000);bind(sockfd, (struct sockaddr *) &serv_addr, sizeof(serv_addr));
```

太好了！我们现在有了与套接字关联的地址。如果我们已经指定了一个无连接协议，例如 UDP，我们可以直接从套接字开始读取。但是，既然我们选择了面向连接的协议 TCP，我们就应该宣布愿意接受传入的连接，然后从这些连接中读取数据。让我们看看这是如何发生的。

## 监听套接字

监听是通过[监听](https://linux.die.net/man/2/listen)系统调用完成的:

```
**int** **listen(int** sockfd, **int** backlog**);**
```

第一个参数是我们想要监听的套接字，第二个参数指定了所谓的 backlog。(由操作系统)为每个套接字创建一个等待传入连接的队列。backlog 表示队列的大小。如果连接请求在队列已满时到达，客户端可能会收到一个错误，或者该请求可能会被忽略(取决于底层协议)。请注意，队列大小可能会受到某些协议细节或操作系统配置的影响，因此它可能不会采用通过`listen`指定的值。

## 接受传入连接

在我们宣布我们想要接受连接之后，最后一部分是实际接受这样的连接。这是通过(惊喜！)的[接受](https://linux.die.net/man/2/accept)系统调用。

```
**int** **accept(int** sockfd, **struct sockaddr** *****addr, **socklen_t** *****addrlen**);**
```

`sockfd`是我们之前监听的插座。参数`addr`是一个指向`sockaddr`结构的指针。这个结构中填充了对等套接字的地址(客户机的地址)，这是通信层所知道的。返回地址的确切格式由套接字的地址族决定。如果我们不关心对等地址，我们可以直接通过`NULL`。

`addrlen`参数是一个值-结果参数:调用者必须初始化它以包含`addr`所指向的结构的大小(以字节为单位)。返回时，它将包含对等地址的实际大小。

## 例子

让我们把它们放在一起。请注意，只有在尝试接受传入连接时出现错误，下面的示例才会退出。

在构建和运行之后，我们可以验证行为是正确的。首先，让我们看看程序正在监听所需的地址:

```
$ ss -tnl src :8000
State      Recv-Q Send-Q   Local Address:Port     Peer Address:Port
LISTEN     0      128                  *:8000                *:*
```

您可以看到我们有一个处于`LISTEN`状态的`TCP`套接字。套接字的本地地址是`*:8000`，对应于我们已经配置的`INADDR_ANY:8000`。`Send-Q`的大小是`128`，由`listen`系统调用的`backlog`参数指定。

如果我们使用`netcat`与我们的程序建立连接，我们会看到“Hello，socket！”消息。

```
$ nc localhost 8000
Hello, socket!
```

我们程序的输出打印了对等体的地址(如果我们是从同一台机器连接的，则为 127.0.0.1):

```
$ ./main
received connection from peer: 127.0.0.1
```

## 摘要

在这篇文章中，我们解释了套接字上的`socket`、`bind`、`listen`和`accept`动作的目的。我们重申一下。

```
**socket** — create a socket endpoint for communication
**bind** — bind a name (address) to a socket
**listen** — listen for connections on a socket
**accept** — accept a connection on a socket
```

在本系列的下一部分中，我们将从客户的角度出发，通过在套接字上启动连接、发送和接收数据以及清理来进行观察。敬请关注。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！