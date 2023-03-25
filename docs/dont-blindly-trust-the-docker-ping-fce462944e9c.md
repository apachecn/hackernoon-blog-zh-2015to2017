# 不要盲目相信码头工人

> 原文：<https://medium.com/hackernoon/dont-blindly-trust-the-docker-ping-fce462944e9c>

我今天在一个阿尔卑斯山的[码头](https://hub.docker.com/_/alpine/)集装箱里玩着古老的`ping`指令。我想为另一个服务执行一个小的负载测试，所以我试验了不同的包大小(`ping -s <size in byte>`)，这给我带来了相当多的与 MTU 相关的挫折(但那是另外一个故事)。

在 docker 上搜索 pings 的时候，我偶然发现了这个论坛的帖子。

![](img/6d81411f134a3b5444ca2720f103418d.png)

Header of forum post about misbehaving pings in docker containers on MAC

我会让你自己读这篇文章，而不是复制这里的所有内容，但本质是: **ping 数据包可能会返回给你，即使你发送 ping 到一个不存在的主机**。

这是真的。我随机选择了一个 IP 地址，并确保它不能从我的桌面上 ping 通。

```
$ ping 11.12.13.14 -c 5
PING 11.12.13.14 (11.12.13.14): 56 data bytes
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
Request timeout for icmp_seq 2
Request timeout for icmp_seq 3
^C
--- 11.12.13.14 ping statistics ---
5 packets transmitted, 0 packets received, 100.0% packet loss
```

然后在 Docker 容器中运行它。

```
$ docker run alpine ping 11.12.13.14 -c 5
PING 11.12.13.14 (11.12.13.14): 56 data bytes
64 bytes from 11.12.13.14: seq=0 ttl=37 time=0.447 ms
64 bytes from 11.12.13.14: seq=1 ttl=37 time=0.469 ms
64 bytes from 11.12.13.14: seq=2 ttl=37 time=0.524 ms
64 bytes from 11.12.13.14: seq=3 ttl=37 time=0.476 ms
64 bytes from 11.12.13.14: seq=4 ttl=37 time=0.461 ms--- 11.12.13.14 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 0.447/0.475/0.524 ms
```

这是个坏消息，因为这意味着你再也不能真正信任`ping`了。您可能有一个运行状况检查，它通过 pings 测试到您的一个生产服务的连接性，并且即使该服务关闭了也一直报告绿色。

更糟糕的是，并不是所有的假 IP 都会出现这种情况，论坛帖子是一年多前创建的。

好消息是，这只发生在 MAC 上的 Docker 上(也许 Windows 也是)，而不是 Linux 上。

![](img/891a5926871928007c1cf149095e7701.png)

Screenshot of the “ghost ping”.