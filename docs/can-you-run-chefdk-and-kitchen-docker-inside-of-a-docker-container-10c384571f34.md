# 你能在 docker 容器中运行 ChefDK 和 kitchen-docker 吗？

> 原文：<https://medium.com/hackernoon/can-you-run-chefdk-and-kitchen-docker-inside-of-a-docker-container-10c384571f34>

我想把我的 ChefDK 设置放在一个容器中，这样它的所有依赖项都与主机上的 Ruby 安装隔离开来，并允许我轻松地并行运行不同的版本来测试升级。

所以我从一个基本的 Dockerfile 开始安装 ChefDK…

```
from ubuntu:16.04RUN apt-get updateRUN apt-get install -y curl
RUN curl -O [https://packages.chef.io/files/stable/chefdk/2.4.17/ubuntu/16.04/chefdk_2.4.17-1_amd64.deb](https://packages.chef.io/files/stable/chefdk/2.4.17/ubuntu/16.04/chefdk_2.4.17-1_amd64.deb)
RUN dpkg -i chefdk_2.4.17-1_amd64.deb
```

如果我们构建这个，然后在里面放一个 bash shell

```
docker build -t chefdk .
docker run -it chefdk bash
```

我们安装了厨师和刀！

```
root@6bb552c50752:/# which chef
/usr/bin/chef
root@6bb552c50752:/# which knife
/usr/bin/knife
```

太好了，现在我需要将我的烹饪书安装到容器中，这样我就可以运行测试厨房了，我将在`/cookbooks`安装它们

```
docker run -it -v /Users/aaronkalair/cookbooks:/cookbooks chefdk
```

这是可行的，但是因为我们没有在 docker 文件中指定用户，所以我们是 root 用户，所以容器中的每个新文件也是 root 用户所有。

```
root@d374bab13add:/cookbooks/teabot# touch test.txt
root@d374bab13add:/cookbooks/teabot# ls -lah test.txt
-rw-r--r-- 1 root root 0 Dec 23 15:28 test.txt
```

为了避免这里的任何问题，让我们在容器中创建一个用户，该用户与主机上的用户具有相同的 UID

在主机上，我的 UID 是 501，这在我测试的 3 台机器上是一致的，所以看起来 Mac 将 501 分配给创建的第一个用户帐户。

```
Aarons-iMac:chefdk aaronkalair$ id
uid=501(aaronkalair) ...
```

因此，我们可以在带有`uid 501`的容器中创建一个名为`aaronkalair`的用户，方法是将其添加到 Dockerfile 的末尾

```
RUN useradd -u 501 aaronkalair
RUN mkdir -p /home/aaronkalair
RUN chown aaronkalair:aaronkalair /home/aaronkalair
USER aaronkalair
```

现在让我们为我的 teabot 食谱试着搭建一个测试厨房。首先我们需要安装宝石

```
root@6c602759d146:/cookbooks/teabot# bundle install
bash: bundle: command not found
```

嗯，我们没有安装 Ruby 或者任何相关的工具，但是我们不需要安装，因为 Chef 自带了它们的嵌入版本。

```
root@6c602759d146:/cookbooks/teabot# ls /opt/chefdk/embedded/bin/... <124 packages including, bundle, gem, ruby etc... >
```

因此，让我们通过将它添加到 docker 文件中，将它放到我们的路径中

```
ENV PATH="/opt/chefdk/embedded/bin:${PATH}"
```

现在让我们再试一次`bundle install`。

因为我在不同的食谱中使用不同版本的 Gems，所以让我们把它们安装到这本食谱的文件夹中，而不是根据我上一次工作的食谱在全球范围内安装不同版本的大杂烩

```
bundle install --path vendor --binstubs
```

不幸的是，这会引发一个关于 extconf 失败的错误

```
Gem::Ext::BuildError: ERROR: Failed to build gem native extension.current directory: /cookbooks/teabot/vendor/ruby/2.4.0/gems/libyajl2-1.2.0/ext/libyajl2
/opt/chefdk/embedded/bin/ruby -r ./siteconf20171223-8-1u0q211.rb extconf.rb
creating Makefile
/cookbooks/teabot/vendor/ruby/2.4.0/gems/libyajl2-1.2.0/ext/libyajl2
extconf.rb:104:in `makemakefiles': unhandled exception
 from extconf.rb:138:in `<main>'extconf failed, exit code 1
```

本机扩展、Makefiles 和 extconf 听起来好像我们缺少一些依赖项来安装 C 依赖项，让我们抓住`build-essentials`看看会发生什么，当我们编辑 Dockerfile 时，让我们别名 bundle 命令。

```
RUN apt-get install -y curl **build-essential****RUN echo "alias bundle-install='/opt/chefdk/embedded/bin/bundle install --path vendor --binstubs'" >> /home/aaronkalair/.bashrc**
```

好了，成功了

```
aaronkalair@3ac49ab61513:/cookbooks/teabot$ bundle-install
....
Bundle complete! 3 Gemfile dependencies, 104 gems now installed.
Bundled gems are installed into ./vendor.
```

现在，我们需要 Docker 从容器内可用，这样厨房-docker 可以制造容器。最简单的方法似乎是将套接字从主机安装到容器中。

因此，让我们将运行命令改为:

```
docker run -it -v /Users/aaronkalair/cookbooks:/cookbooks -v **/var/run/docker.sock:/var/run/docker.sock** chefdk
```

并在容器中安装 Docker cli

```
RUN apt-get install -y curl build-essential **docker.io**
```

这使我们能够访问容器内部的 Docker CLI，但它似乎无法与主机上的守护程序对话

```
aaronkalair@4280bf11224b:/$ docker ps
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get [http://%2Fvar%2Frun%2Fdocker.sock/v1.26/containers/json](http://%2Fvar%2Frun%2Fdocker.sock/v1.26/containers/json): dial unix /var/run/docker.sock: connect: permission denied
```

看看它的权限就知道为什么了

```
aaronkalair@4280bf11224b:/$ ls -lah /var/run/docker.sock
srw-rw---- 1 root staff 0 Dec 22 11:32 /var/run/docker.sock
```

只有`root`用户或`staff`组中的用户可以读写套接字，我将通过将我的`aaronkalair`用户添加到 staff 组来修复这个问题。但是要小心你在这里做的事情，因为任何访问 Docker 套接字的人都有对主机的根访问权。

```
RUN usermod -a -G staff aaronkalair
```

现在我们可以和码头工人对话了

```
aaronkalair@43db476d04f7:/$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
43db476d04f7        chefdk              "/bin/bash"         2 seconds ago       Up 1 second                             upbeat_panini
```

所以，让我们试着融合我们的测试厨房吧！

```
aaronkalair@43db476d04f7:/cookbooks/teabot$ ./bin/kitchen converge
....Lots of Good Things...
 **Failed to complete #create action: [Cannot assign requested address - connect(2) for [::1]:32776] on default-ubuntu-1604**
```

奇怪了，`kitchen list`光说`Errno::EADDRNOTAVAIL`也没多大帮助

难道我真的很不走运，别的什么东西用了 32776 作为它的重要端口？让我们安装`netstat`看看。

```
aaronkalair@43db476d04f7:/cookbooks/teabot$ apt-get install net
-toolsE: Could not open lock file /var/lib/dpkg/lock - open (13: Permission denied)
E: Unable to lock the administration directory (/var/lib/dpkg/), are you root?
```

糟糕，我们需要以 root 身份进入容器来调试这个问题

```
docker run -it **-u root** -v /Users/aaronkalair/cookbooks:/cookbooks -v /var/run/docker.sock:/var/run/docker.sock chefdk
```

然后`netstat`只显示了端口`50332`上的一个连接

```
root@e075956d7a2b:/# netstat
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 e075956d7a2b:50332      keeton.canonical.c:http TIME_WAIT
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node   Path
```

有趣的是，我们正在 Docker 里面做一些奇怪的 Docker 东西，也许它在主机上使用？

```
Aarons-iMac:~ aaronkalair$ netstat | grep 32776 
```

不，那里也没用。

我们能在厨房外自己绑定到端口`32776`吗？

```
root@e075956d7a2b:/# nc -l 32776root@e075956d7a2b:/# nc -l 0.0.0.0 32776 
```

是的，这一切都很好

我们能不能`curl`这个霸占端口的神奇东西`32776`？

```
root@e075956d7a2b:/# curl -v localhost:32776
* Rebuilt URL to: localhost:32776/
*   Trying 127.0.0.1...
* TCP_NODELAY set
* connect to 127.0.0.1 port 32776 failed: Connection refused
*   Trying ::1...
* TCP_NODELAY set
*** Immediate connect fail for ::1: Cannot assign requested address
*   Trying ::1...** * TCP_NODELAY set
* Immediate connect fail for ::1: Cannot assign requested address
* Failed to connect to localhost port 32776: Connection refused
* Closing connection 0
curl: (7) Failed to connect to localhost port 32776: Connection refused
```

有趣的是，`::1:`给了我们和测试厨房一样的错误，这是一些奇怪的 IPv6 东西吗？

快速谷歌确认 IPv6 仅在你指定一个特殊标志的情况下才在 Docker 中工作—[https://docs . Docker . com/engine/user guide/networking/default _ network/IPv6/](https://docs.docker.com/engine/userguide/networking/default_network/ipv6/)

```
By default, the Docker daemon configures the container network for IPv4 only. You can enable IPv4/IPv6 dualstack support by running the Docker daemon with the --ipv6flag.
```

好吧，我实际上并不需要 IPv6，那么我如何阻止它尝试使用它呢？井`localhost`在`/etc/hosts`中定义为

```
root@e075956d7a2b:/# cat /etc/hosts
127.0.0.1 localhost
::1 localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2 e075956d7a2b
```

所以，如果我只是用核武器攻击那些 IPv6 线路，也许会没事？

(旁注:显然编辑`/etc/gai.conf`可以在 IPv4 行为之前改变 IPv6—[https://askubuntu.com/a/38468](https://askubuntu.com/a/38468))

```
root@e075956d7a2b:/# vi /etc/hosts
root@e075956d7a2b:/# cat /etc/hosts
127.0.0.1 localhost
172.17.0.2 e075956d7a2b
```

让我们再次尝试收敛(调试日志记录可能会有所帮助)

```
root@e075956d7a2b:/cookbooks/teabot# ./bin/kitchen converge -l debug
...
D      [SSH] opening connection to kitchen@localhost<{:user_known_hosts_file=>"/dev/null", :port=>32776, :compression=>false, :compression_level=>0, :keepalive=>true, :keepalive_interval=>60, :timeout=>15, :keys_only=>true, :keys=>["/cookbooks/teabot/.kitchen/docker_id_rsa"], :auth_methods=>["publickey"], :verify_host_key=>false}>
$$$$$$ [SSH] connection failed, terminating (#<Errno::ECONNREFUSED: Connection refused - connect(2) for 127.0.0.1:32776>)
```

好吧，那么这证实了上述问题是由于试图在一个 Docker 环境中使用 IPv6，而不是为它配置的。

现在我们有了一个新问题，到端口`32776`的 SSH 连接被拒绝。

有什么东西在本地监听那个端口吗？回到我们的朋友`netstat`

```
root@e075956d7a2b:/cookbooks/teabot# netstat --listening
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
Active UNIX domain sockets (only servers)
Proto RefCnt Flags       Type       State         I-Node   Path
```

没有。

test-kitchen 真的成功制作了一个运行 SSH 守护进程的容器吗？

```
root@e075956d7a2b:/cookbooks/teabot# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                   NAMES
9ca4e56ab0b4        2fe546b254ce        "/usr/sbin/sshd -D..."   56 minutes ago      Up About an hour    0.0.0.0:32776->22/tcp   defaultubuntu1604-nologin-43db476d04f7-hds4zcup
```

看起来确实是这样，如果我们执行到那个容器中，那么`sshd`实际上正在运行吗？

```
root@e075956d7a2b:/cookbooks/teabot# docker exec -it 9ca4e56ab0b4 bash
root@9ca4e56ab0b4:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 16:17 ?        00:00:00 /usr/sbin/sshd -D -o UseDNS=no -o UsePAM=no -o PasswordAuthentication=yes
root         6     0  0 17:15 pts/0    00:00:00 bash
root        16     6  0 17:15 pts/0    00:00:00 ps -ef
```

没错。

查看上面`docker ps`的输出，我们可以看到`32776`来自哪里，它是容器中的 22 映射到主机上的端口。

但我们不是在主机上，而是在 Dockers 自己网络的一个容器内。

好了，这就是我们的情况:

*   kitchen-docker 制作了一个容器，它可以在里面运行`chef`,但是它假设我们在主机上运行，并且会有`sshd` 在端口处监听 docker 映射的`22`在`localhost`上
*   我们在 Docker 创建的网络上的 Docker 容器中，而不是主机中，因此不能使用端口映射`22 -> 32776`,无论它在哪个端口上，对我们来说都不在本地主机上

幸运的是，所有这些问题都是可以解决的，我们与运行`sshd`的容器在同一个网络上，所以我们应该能够通过 IP 地址与 IP 对话。

```
root@e075956d7a2b:/cookbooks/teabot# docker inspect 9ca4e56ab0b4 | grep -i ipaddress
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.3",
                    "IPAddress": "172.17.0.3",
```

一个快速的理智检查，看看我们是否能和它说话

```
In the container created by kitchen-dockerroot@9ca4e56ab0b4:/# nc -l -p 5000
PING
^C
```

在运行 chefdk 的容器中

```
root@e075956d7a2b:/cookbooks/teabot# telnet 172.17.0.3 5000
Trying 172.17.0.3...
Connected to 172.17.0.3.
Escape character is '^]'.
PING
```

太好了，现在来修改厨房码头

这里设置端口和主机名—[https://github . com/test-kitchen/kitchen-docker/blob/master/lib/kitchen/driver/docker . Rb # L124](https://github.com/test-kitchen/kitchen-docker/blob/master/lib/kitchen/driver/docker.rb#L124)

因此，让我们快速黑入 IP 地址和端口 22，检查它是否工作

```
root@e075956d7a2b:/cookbooks/teabot# vi vendor/ruby/2.4.0/gems/kitchen-docker-2.6.0/lib/kitchen/driver/docker.rb<some quick edits>
```

让我们看看这是否可行:

```
root@e075956d7a2b:/cookbooks/teabot# ./bin/kitchen converge -l debug
...D      [SSH] kitchen@172.17.0.3<{:user_known_hosts_file=>"/dev/null", :port=>"22", :compression=>false, :compression_level=>0, :keepalive=>true, :keepalive_interval=>60, :timeout=>15, :keys_only=>true, :keys=>["/cookbooks/teabot/.kitchen/docker_id_rsa"], :auth_methods=>["publickey"], :verify_host_key=>false}> (sudo -E sh -c '[SSH] Established
...
```

好了，成功了！

我为 kitchen-docker 编写了这个补丁来完成上述工作，而无需在 IP 地址中进行硬编码—[https://github . com/test-kitchen/kitchen-docker/pull/283/files](https://github.com/test-kitchen/kitchen-docker/pull/283/files)

(旁注:给定 Docker 容器的 ID，获取其 IP 地址的一个非常简单的方法是)

```
docker inspect --format '{{ .NetworkSettings.IPAddress }}' <container id>
```

所以我们可以在 docker 容器中运行 ChefDK 和 kitchen-docker(对 kitchen-docker 做了一些小的改动)。为了完成这个任务，我在容器中安装了`vim`，并把我的`vim`、`git`和`chef`配置从主机安装到容器中，这样我就不用离开容器在编辑和测试之间切换了。

(虽然 cookbooks 是从主机挂载的，所以您可以在主机上编辑它们，然后切换到容器进行测试，如果您愿意的话)

你可以在我的 Github repo 上看到最终的 docker 文件和运行命令，这里是[https://Github . com/AaronKalair/docker _ apps/tree/master/chef dk](https://github.com/AaronKalair/docker_apps/tree/master/chefdk)

[在 Twitter @AaronKalair 上关注我](https://twitter.com/aaronkalair)