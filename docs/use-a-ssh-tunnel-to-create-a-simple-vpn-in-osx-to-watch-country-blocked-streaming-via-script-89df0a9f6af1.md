# 使用 ssh 隧道在 OSX 创建一个简单的 VPN，通过脚本观看国家禁止的流媒体。

> 原文：<https://medium.com/hackernoon/use-a-ssh-tunnel-to-create-a-simple-vpn-in-osx-to-watch-country-blocked-streaming-via-script-89df0a9f6af1>

> 有时候你在错误的时间出现在错误的地方。

![](img/e0599fdee9bfdee0ae36dee92ac94480.png)

当您可以访问 ssh 服务器时，就不需要昂贵的 VPN 服务器了。这可能是家里的一台机器，一个小型的 vps 服务器，甚至是一个共享的虚拟主机账户。

> 提示:在端口 443 运行您自己的 ssh 服务器，以避开任何公司防火墙的限制。

## 用 SSH 隧道当穷人的 vpn

ssh -D 创建一个本地 SOCKS 代理，并通过 ssh 连接在该端口上转发所有流量。

它超级简单！

```
$ ssh -D 8000 user@ssh-server.dyndns.org
```

现在有一个 SOCKS 代理在 localhost:8000 下运行。

## 通过终端在 OSX 设置一个 SOCKS 代理

现在您必须配置您的网络设置来使用这个 SOCKS 代理。当然，我们希望在我们的脚本中实现自动化。

```
$ sudo networksetup -setsocksfirewallproxy "Wi-Fi" localhost 8000
```

## 最后，我们把所有东西打包成一个不错的剧本

```
$ ./tunnel.sh { start | stop }
```

结束了。

## 要注意！

默认情况下，您的终端命令将**而不是**使用这个 SOCKS 代理。对于一些(wget，curl..)有一个使用代理的配置标志。

但是如果你想让所有的流量都通过隧道，你应该看看[隧道](https://github.com/apenwarr/sshuttle)。

但是对我来说，使用 sshuttle 比简单的 ssh 隧道要慢得多。如果您遇到这个问题，请尝试使用选项`- no-latency-control '运行 sshuttle。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)