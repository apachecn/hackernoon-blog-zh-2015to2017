# 方便的 Varnish 4 top 和 log 命令

> 原文：<https://medium.com/hackernoon/handy-varnish-4-top-and-log-commands-d650efd45c15>

升级到 [Varnish](https://hackernoon.com/tagged/varnish) 4 后，我发现我的旧 Varnish 3 top 和 log 命令不再工作，并发现很难找到 Varnish 4 top 和 [log](https://hackernoon.com/tagged/log) 示例的列表。

下面是一些我经常使用并发现有用的 Varnish 4 top 和 log 命令。

**查看最常请求的后端 URL**
`sudo varnishtop -i BereqURL`

**查看顶级 IP 地址**
`sudo varnishtop -i ReqStart`

**查看前 X 位转发 IP 地址**
`sudo varnishtop -C -I ReqHeader:X-Forwarded-For`

**查看顶级用户代理**
`sudo varnishtop -C -I ReqHeader:User-Agent`

**查看特定主机的请求**
`varnishlog -q “BereqHeader ~ ‘^Host: example.com’”`

**查看对特定 URL 的请求**
`varnishlog -q “ReqURL ~ ‘/my/path/’”`