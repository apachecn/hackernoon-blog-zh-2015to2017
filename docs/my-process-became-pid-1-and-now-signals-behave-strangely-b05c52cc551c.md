# 我的进程变成 PID 1，现在信号表现奇怪

> 原文：<https://medium.com/hackernoon/my-process-became-pid-1-and-now-signals-behave-strangely-b05c52cc551c>

或者让我们编写自己的初始化进程

当您的流程在 Docker 容器中作为 [PID](https://hackernoon.com/tagged/pid) 1 运行时，信号处理的行为可能与您预期的不同。

首先让我们检查当一个进程在“正常”系统上不是 PID 1 时会发生什么。

一个简单的 [Python](https://hackernoon.com/tagged/python) 进程只是休眠

```
Aarons-iMac:bin aaronkalair$ cat mypy.py
import subprocesssubprocess.call(["sleep", "100"])
```

如果我们运行它并发送`SIGTERM`

```
Aarons-iMac:init-proc aaronkalair$ ps -ef | grep python
  501 **14013**  6588   0  2:08pm ttys004    0:00.02 **python mypy.py**Aarons-iMac:bin aaronkalair$ **kill 14013**
**Terminated: 15**
```

它被终止了，这没什么奇怪的

现在让我们在 Docker 容器中将它作为 PID 1 运行

```
Aarons-iMac:bin aaronkalair$ cat Dockerfile
from ubuntu:16.04RUN apt-get update
RUN apt-get install -y python
COPY mypy.py /srv/CMD ["python", "/srv/mypy.py"]
```

运行这个容器，执行，然后发送相同的信号

```
Aarons-iMac:init-proc aaronkalair$ docker exec -it 0229aa205b48 bashroot@0229aa205b48:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         **1**     0  0 14:15 ?        00:00:00 **python /srv/mypy.py**
root         7     1  0 14:15 ?        00:00:00 sleep 100root@0229aa205b48:/# **kill 1**root@0229aa205b48:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         **1**     0  0 14:15 ?        00:00:00 **python /srv/mypy.py**
root         7     1  0 14:15 ?        00:00:00 sleep 100
```

现在什么都没发生！

让我们用一个类似的 Go 流程来尝试一下

```
package mainimport (
    "time"
)func main() {
    time.Sleep(time.Duration(100000) * time.Millisecond)
}
```

将它放入 Docker 容器，运行它，执行并发送它`SIGTERM`

```
Aarons-iMac:init-proc aaronkalair$ docker exec -it e6ccf11be060 bashroot@e6ccf11be060:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         **1**     0  0 14:28 ?        00:00:00 **./srv/sleep-spawner**root@e6ccf11be060:/# **kill 1**root@e6ccf11be060:/# **Aarons-iMac:init-proc aaronkalair$**
```

它被杀死了，就像它没有作为 PID 1 运行时一样

那么这到底是怎么回事？

PID 1 在 Linux 中很特殊，除了别的以外，它忽略任何信号，除非明确声明了该信号的处理程序。来自 Docker docs—[https://docs.docker.com/engine/reference/run/#foreground](https://docs.docker.com/engine/reference/run/#foreground)

> ***注意****:Linux 对容器内运行为 PID 1 的进程进行了特殊处理:它会忽略任何带有默认动作的信号。因此，进程不会在* `*SIGINT*` *或* `*SIGTERM*` *上终止，除非它被编码为这样做。*

我们可以在我们想要在 Docker 容器中运行的每个进程中为这些信号定义处理程序，但是这需要大量的工作，而且我们可能没有源代码来完成。此外，PID 1 还有其他职责，我们将在后面探讨。

因此，我们可以运行一个不同的进程作为 PID 1，并让它向我们想要运行的实际进程发送代理信号，并执行标准 init 进程的其他任务

有许多解决方案可以做到这一点，例如

https://github.com/Yelp/dumb-init 的叫声`dumb-init`

`Tini`随 Docker—[https://docs . Docker . com/engine/reference/run/# specify-an-init-process](https://docs.docker.com/engine/reference/run/#specify-an-init-process)

你可以通过搜索找到更多。

但是我要写我自己的…

让我们从基础开始，我需要一个程序，用另一个进程的名字来执行它

```
func main() {
    cmd := exec.Command(os.Args[1], os.Args[2:]...)
    err := cmd.Start()
    if err != nil {
        panic(err)
    }
    err = cmd.Wait()
    if err != nil {
        panic(err)
    }
}
```

关于我们如何做到这一点，需要注意一些重要的事情，因为这在以后会很重要。

在我们`Start()`我们称之为`Wait()`的新进程之后，这很重要，它将一直阻塞，直到命令退出，并且一旦它退出，就清理与之相关的任何资源。

无法在您生成的进程上`wait`会导致僵尸进程，一旦它们执行完消耗了一些资源，就会停滞不前。

来自手册页—[http://man7.org/linux/man-pages/man2/waitpid.2.html#NOTES](http://man7.org/linux/man-pages/man2/waitpid.2.html#NOTES)

> 一个终止了，却没有被等待的孩子，就变成了“僵尸”。内核维护关于僵尸进程的最小信息集(PID、终止状态、资源使用信息),以便允许父进程稍后执行等待以获得关于子进程的信息。只要僵尸没有通过等待从系统中移除，它就会消耗内核进程表中的一个槽，如果这个表填满了，就不可能创建进一步的进程。

因此，让我们尝试一下我们的新信号代理，如果我们在容器中运行它…

```
CMD ["./srv/init-proc", "/srv/sleep-spawner", "1"]
```

我们可以看到我们的代理进程现在是 PID 1，并产生了睡眠产卵器

```
root@36c4892039db:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         **1**     0  0 17:45 ?        00:00:00 **./srv/init-proc /srv/sleep-spawner 1**
root        **11**     1  0 17:45 ?        00:00:00 **/srv/sleep-spawner 1**
```

好的，下一步是注册我们对所有可能的信号感兴趣

```
func main() {
    signalChannel := make(chan os.Signal, 2)
    signal.Notify(signalChannel)
    pid := -1 go sigHandler(&pid, signalChannel) cmd := exec.Command(os.Args[1], os.Args[2:]...)
    err := cmd.Start()
    pid = cmd.Process.Pid if err != nil {
        panic(err)
    }
    err = cmd.Wait()
    if err != nil {
        panic(err)
    }
}
```

用`sigHandler`定义为:

```
func sigHandler(pid *int, signalChannel chan os.Signal) {
    var sigToSend syscall.Signal = syscall.SIGHUP
    for {
        sig := <-signalChannel
        switch sig {
            // #1 - Sent went the controlling terminal is closed,          typically used by daemonised processes to reload config
            case syscall.SIGHUP:
                sigToSend = syscall.SIGHUP
            // #2 - Like pressing CTRL+C
            case syscall.SIGINT:
                sigToSend = syscall.SIGINT
            ..... 
                repeat for all signals
        }
        **syscall.Kill(*pid, sigToSend)**
    }
}
```

它只是打开 Go 支持的所有信号—[https://golang.org/pkg/syscall/#pkg-constants](https://golang.org/pkg/syscall/#pkg-constants)

然后使用`kill`系统调用将信号发送给正在运行的进程。

现在让我们用它来运行我们的 Python 程序，看看它是否正确地处理了 SIGTERM。

```
Aarons-iMac:init-proc aaronkalair$ docker exec -it 579ef1d3ce77 bashroot@579ef1d3ce77:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         **1**     0  0 18:33 ?        00:00:00 **./srv/init-proc** python /srv/mypy.py
root        **13**     1  0 18:33 ?        00:00:00 **python /srv/mypy.py**
root        14    13  0 18:33 ?        00:00:00 sleep 100root@579ef1d3ce77:/# **kill 1**root@579ef1d3ce77:/# **Aarons-iMac:init-proc aaronkalair$**
```

而且很管用！

现在让我们来关注 PID 1 负责的另一件事，清理僵尸进程。

想象一下这个场景

A —产卵-> B —产卵-> C

现在如果 B 在 C 之前死亡或退出，C 就成了孤儿进程，那么 C 现在的父进程是谁？

操作系统负责将孤立进程重命名为 PID 1，所以现在看起来像这样

A —是-> C 的父级

现在当 C 退出时，A 将接收到`SIGCHILD`信号，并负责调用 C 上的`wait`来清理这个僵尸进程。

因此，让我们将这个逻辑添加到 SIGCHILD 案例中:

```
case syscall.SIGCHLD:
    var status syscall.WaitStatus
    var rusage syscall.Rusage
 **syscall.Wait4(-1, &status, syscall.WNOHANG, &rusage)**    sigToSend = syscall.SIGCHLD
```

`-1`意味着等待任何一个子进程改变状态，而不是等待一个特定的子进程改变状态，因为我们不知道收到信号时已经退出的进程的 ID

`WNOHANG`表示如果没有子进程改变了状态，不要阻塞等待，立即返回

对终止的子进程执行`wait`会清理它的资源，防止它成为僵尸进程

来自`wait`联机帮助页—[http://man7.org/linux/man-pages/man2/waitpid.2.html](http://man7.org/linux/man-pages/man2/waitpid.2.html)

> 在终止孩子的情况下，执行等待允许系统释放与孩子相关联的资源；如果不执行等待，那么被终止的子进程将保持“僵死”状态

现在只剩下一个案子要处理了想象一下:

A —产卵-> B —产卵-> C

现在 C 退出了，但是 B 没有调用 wait on

a-父-> B-父-> C(已失效的僵停进程)

`wait`只作用于子进程，所以不管我们的初始化进程 A 调用`wait`多少次，它都不会清理 C 正在使用的资源。(请注意，`SIGCHILD`只会被发送到 B，所以 A 甚至不会意识到 C 的退出)

现在 B 退出，A 接收到`SIGCHILD`调用`wait`，B 被很好地清理。

c 现在是一个孤儿，被重配置为 A，所以我们有

A —是-> C(已失效的僵尸进程)的父进程

我们可以看到上面的动作，对我们的睡眠程序做了一些修改，产生了父母在孩子之前退出并且不调用`wait`的过程

```
func main() {
    MAX_LEVEL := 4level, err := strconv.Atoi(os.Args[1])
    if err != nil {
        panic(err)
    }// We'll have a bunch of processes that immediately exit at the max level
    if level == MAX_LEVEL {
        return
    }// Need the top level to outlive the others, otherwise the container would exit and you wouldn't be able to inspect the process tree
    sleepTime := 0
    if level == 1 {
        sleepTime = 20000000
    } else {
    // Generate proceses where children sleep for longer than there parents so parents exit first without waiting on the children showing what happens to orphan / zombie processes
        sleepTime = level * 1000
    }level += 1
    for i := 0; i < 2; i++ {
        // Spawn a command and intentionally dont wait on it
        err := exec.Command("/srv/sleep-spawner", strconv.Itoa(level)).Start()
        if err != nil {
            panic(err)
        }
    }
    time.Sleep(time.Duration(sleepTime) * time.Millisecond)
}
```

在 Github 上可以找到，这里是[https://github.com/AaronKalair/sleep-spawner](https://github.com/AaronKalair/sleep-spawner)

如果我们运行它，我们可以看到流程树的样子:

```
Aarons-iMac:init-proc aaronkalair$ docker exec -it 854a232d4b89 bash
root@854a232d4b89:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 22:13 ?        00:00:00 ./srv/init-proc /srv/sleep-spawner 1
root        12     1  0 22:13 ?        00:00:00 /srv/sleep-spawner 1
root        17    12  0 22:13 ?        00:00:00 **[sleep-spawner] <defunct>**
root        22    12  0 22:13 ?        00:00:00 **[sleep-spawner] <defunct>**
root        32     1  0 22:13 ?        00:00:00 **[sleep-spawner] <defunct>**
```

在我们当前的实现中，这种情况将永远存在，因此我们需要对其稍加修改，以处理如下情况:

```
case syscall.SIGCHLD:
    var status syscall.WaitStatus
    var rusage syscall.Rusage
    for {
        retValue, err := syscall.Wait4(-1, &status, syscall.WNOHANG, &rusage)
        if err != nil {
            panic(err)
        }
        if retValue <= 0 {
            break
        }
   }
   sigToSend = syscall.SIGCHLD
```

当与`WNOHANG`结合使用时，我们利用`wait4`的返回值在每次得到`SIGCHILD`信号时循环调用它。

同样来自手册页(wait4 的返回值符合 wait PID—[http://man7.org/linux/man-pages/man2/waitpid.2.html](http://man7.org/linux/man-pages/man2/waitpid.2.html))

> 成功时，返回状态已更改的子进程的进程 ID；如果指定了 **WNOHANG** 并且存在由 *pid* 指定的一个或多个子(ren ),但是还没有改变状态，则返回 0。出错时，返回-1。

所以我们可以一直调用`Wait4`，直到我们得到一个小于或等于 0 的返回值，知道它正在清理已退出的进程。

现在，如果我们在容器内部运行这个 exec 并使用`ps`进行检查

```
Aarons-iMac:init-proc aaronkalair$ docker exec -it 30f13d4e53bd bash
root@30f13d4e53bd:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 22:05 ?        00:00:00 ./srv/init-proc /srv/sleep-spawner 1
root        12     1  0 22:05 ?        00:00:00 /srv/sleep-spawner 1
root        17    12  0 22:05 ?        00:00:00 [sleep-spawner] <defunct>
root        18    12  0 22:05 ?        00:00:00 [sleep-spawner] <defunct> 
```

我们可以看到 PID 1 的僵尸已经被清理了！

现在我们有了它，我们创建了一个基本的 init 进程，它允许我们向运行在 Docker 容器中的进程发送信号，让它们像在容器外一样工作，并且能够清理僵尸进程！

点击这里查看完整源代码—[https://github.com/AaronKalair/init-proc](https://github.com/AaronKalair/init-proc)

[在推特@AaronKalair 上关注我](http://twitter.com/aaronkalair)