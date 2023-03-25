# tmux 实践:使用 SSH 远程隧道和 systemd 服务从远程会话复制文本

> 原文：<https://medium.com/hackernoon/tmux-in-practice-copy-text-from-remote-session-using-ssh-remote-tunnel-and-systemd-service-dd3c51bca1fa>

## 将文本从远程会话复制到本地剪贴板的另一种方法

在“tmux 实践”系列的前一篇文章[中，我们已经讨论了从](/@alexeysamoshkin/tmux-in-practice-integration-with-system-clipboard-bcd72c62ff7b) [tmux](https://hackernoon.com/tagged/tmux) 会话向系统剪贴板共享复制文本的各种解决方案。虽然这对于本地会话来说很容易设置(只需将选定的文本通过管道发送到`pbcopy`或`xclip`或`xsel`)，但是当您使用远程 tmux 会话时，事情就变得复杂了。您需要一些机制来将数据从远程机器传输到本地系统的剪贴板。如果您的终端仿真器处理 [OSC 52 ANSI 转义序列](https://sunaku.github.io/tmux-yank-osc52.html)，那么您是幸运的。然而，只有少数终端模拟器支持这个特性:它可以在 iTerm 的 OSX 上开箱即用，并且很可能在 [Linux](https://hackernoon.com/tagged/linux) 上失败(除非您使用的是基本的 xterm)。

今天，让我们探索替代解决方案。它由以下部分组成:

1.  在本地机器上，我们将设置 systemd 服务，监听网络套接字，将接收到的任何输入通过管道传输到`xclip`，从而将其存储在系统剪贴板中。
2.  设置 SSH 远程隧道，连接远程机器上的端口和本地机器上的端口，我们的服务在本地机器上监听。
3.  更改远程 tmux 配置，将选定的文本通过管道传输到远程机器上的网络端口，以便通过 SSH 远程隧道传输到本地机器。

# 套接字激活的 xclip 系统服务

所以我们需要在某个端口上设置网络监听器。当建立连接时，任何输入都应该通过管道传输到`xclip`以存储在系统剪贴板上。这个服务将永久运行，所以我们的选择是创建一个 systemd 套接字激活的服务。如果你在 OSX，使用 launchd 而不是 systemd(见下面关于 OSX 的段落)。

最简单的方法是创建两个单元文件:服务单元和套接字单元。把你的单位文件放在`/etc/systemd/system`目录下。让我们从`/etc/systemd/system/xclip.socket`文件开始:

```
[Unit]
Description=Network copy backend for tmux based on xclip[Socket]
ListenStream=19988
Accept=yes[Install]
WantedBy=sockets.target
```

单元文件只是说这是一个监听端口 19988 的网络套接字。服务设计因`Accept`设置的不同而有很大差异:

*   `yes`，systemd 将接受传入的连接，并将连接套接字传递给目标服务。然后，套接字可以连接到服务进程的标准输入和标准输出文件描述符。服务在第一次连接时被延迟启动。将为每个传入的连接生成新的服务实例(模板化的单元在幕后使用)
*   `no`，systemd 将不接受连接，监听套接字将传递给服务。程序必须定制，以便它可以处理这些插座(使用`sd_listen_fds`功能)。无论连接数多少，都只会产生一个服务实例(单一服务)。服务被延迟激活，并在第一次传入连接时启动。

让我们坚持使用`Accept=yes`设计，因为它非常简单，只需使用`xclip`命令，无需任何修改，只需将套接字连接到 xclip 的标准输入和标准输出描述符。

现在，让我们创建`/etc/systemd/system/xclip@.service`。注意，`Accept=yes`一提出这个要求，就应该是模板单元文件。

```
[Unit]
Description=Copy backend service piping input to xclip[Service]
Type=simple
ExecStart=/usr/bin/xclip -i -f -selection primary | /usr/bin/xclip -i -selection clipboard
StandardInput=socket
StandardOutput=socket
```

我们使用熟悉的 xclip 命令将数据存储在主选择和剪贴板选择中。

现在，让我们启用并启动我们的套接字单元。`enable` -表示它将在下次系统启动时自动启动，`start` -表示我们现在手动启动它。

```
$ sudo systemctl enable xclip.socket
Created symlink from /etc/systemd/system/sockets.target.wants/xclip.socket to /etc/systemd/system/xclip.socket.$ sudo systemctl start xclip.socket$ sudo systemctl status xclip.socket● xclip.socket - XClip socket
   Loaded: loaded (/etc/systemd/system/xclip.socket; enabled; vendor preset: disabled)
   Active: active (listening) since Mon 2017-11-27 15:07:12 EET; 3s ago
   Listen: [::]:19988 (Stream)
 Accepted: 0; Connected: 0Nov 27 15:07:12 centos7 systemd[1]: Listening on XClip socket.
Nov 27 15:07:12 centos7 systemd[1]: Starting XClip socket.
```

我们看到我们的插座单元启动了。`Accepted`表示自服务启动以来建立的连接总数，`Connected`表示当前活动连接的数量。让我们确保端口 19988 正在侦听:

```
$ ss -tnl '( sport = 19988 )'State      Recv-Q Send-Q Local Address:Port  Peer Address:Port
LISTEN     0      128    :::19988            :::*
```

我们可以使用`netcat`来测试我们的服务。你发送给它的任何数据都应该在系统剪贴板中。在我们深入之前测试一下。

```
echo "text to copy" | nc localhost 19988
```

注意，如果您对服务实例单元状态感兴趣，您将不会通过`systemctl list-units`找到任何正在运行的实例。这是因为一旦 xclip 进程退出，它就会产生并立即退出。

# SSH 远程隧道

当您连接到远程机器时，使用以下命令设置 SSH 远程隧道:

```
ssh -R 19988:localhost:19988 alexeys@192.168.33.101
```

或者您可以在您的`~/.ssh/config`文件中设置一次:

```
Host vb_ubuntu14
  Hostname 192.168.33.100
  User alexeys
  IdentityFile ~/.ssh/alexeys_at_vb_ubuntu14
  RemoteForward 19988 localhost:19988
```

然后就:

```
ssh vb_ubuntu14
```

SSH 远程隧道允许远程网络上的应用程序通过特定端口(在我们的例子中，localhost:19988)与本地网络上的服务对话。为了避免混乱，我们在本地和远程机器上使用相同的端口号。

# tmux 配置

现在，当我们准备好所有的部分时，我们可以将它们连接到我们在“tmux 实践”的前一部分中制作的`~/.tmux.conf.`let extend`yank.sh`文件:

```
*# Resolve copy backend: pbcopy (OSX), reattach-to-user-namespace (OSX), xclip/xsel (Linux), or network service**# get data either form stdin or from file* buf=$(cat "$@")copy_backend=""
if is_app_installed pbcopy; then
  copy_backend="pbcopy"
elif is_app_installed reattach-to-user-namespace; then
  copy_backend="reattach-to-user-namespace pbcopy"
elif [ -n "${DISPLAY-}" ] && is_app_installed xsel; then
  copy_backend="xsel -i --clipboard"
elif [ -n "${DISPLAY-}" ] && is_app_installed xclip; then
  copy_backend="xclip -i -f -selection primary | xclip -i -selection clipboard"
elif [ "$(ss -n -4 state listening "( sport = 19988 )" | tail -n +2 | wc -l)" -eq 1 ]; then
  copy_backend="nc localhost 19988"
fi*# if copy backend is resolved, copy and exit* if [ -n "$copy_backend" ]; then
  printf "$buf" | eval "$copy_backend"
  exit;
fi
```

这里我们有分支逻辑来选择剪贴板后端，我们应该在那里传输一个选定的文本。后者`elif`检查是否有人监听端口`19988`，并将数据 netcats 到该端口。

`~/.tmux.conf`中的按键:

```
yank="~/.tmux/yank.sh"bind -T copy-mode-vi Enter send-keys -X copy-pipe-and-cancel “$yank”
bind -T copy-mode-vi Y send-keys -X copy-pipe-and-cancel “$yank; tmux paste-buffer”
bind-key -T copy-mode-vi D send-keys -X copy-end-of-line \*;* run "tmux save-buffer - | $yank"
bind-key -T copy-mode-vi A send-keys -X append-selection-and-cancel \*;* run "tmux save-buffer - | $yank"
```

# 限制

老实说，解决方案并不像微风一样轻巧。此外，它也有局限性。

如果您使用相同的端口转发配置启动多个 SSH 会话(从相同的本地机器或不同的机器)到相同的远程机器，则只有第一个连接将正常运行文本复制。在这种情况下，您需要将不同的远程端口隧道连接到同一个本地端口，并更改`tmux.conf`来探测几个端口。

# OSX 怎么了？

上面所说的一切对 OSX 来说都是正确的，除了你将使用 launchd 而不是 systemd，并且使用`pbcopy` 而不是`xclip.`

以下是等效启动服务的示例:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "[http://www.apple.com/DTDs/PropertyList-1.0.dtd](http://www.apple.com/DTDs/PropertyList-1.0.dtd)">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>local.pbcopy</string>
    <key>UserName</key>
    <string>asamoshkin</string>
    <key>Program</key>
    <string>/usr/bin/pbcopy</string>
    <key>Sockets</key>
    <dict>
        <key>Listeners</key>
        <dict>
            <key>SockNodeName</key>
            <string>localhost</string>
            <key>SockServiceName</key>
            <string>19988</string>
        </dict>
    </dict>
    <key>inetdCompatibility</key>
    <dict>
        <key>Wait</key>
        <false/>
    </dict>
</dict>
</plist>
```

要安装并启动它:

```
$ launchctl load local.pbcopy.plist
$ launchctl start local.pbcopy
```

通过查看我的 [tmux-config](https://github.com/samoshkin/tmux-config) repo，你可以看到所有这些东西在起作用。

# 资源和链接

MAC OS——如何使用 iTerm2 从远程 shell 复制到 OSX 剪贴板？—提问不同—[https://apple . stack exchange . com/questions/257609/how-do-I-copy-to-the-OS x-clipboard-from-a-remote-shell-using-ITER m2](https://apple.stackexchange.com/questions/257609/how-do-i-copy-to-the-osx-clipboard-from-a-remote-shell-using-iterm2)

macos —在远程 tmux 会话和本地 macos 粘贴板之间同步粘贴板—超级用户—[https://Super User . com/questions/407888/Synchronize-pasteboard-between-remote-tmux-session-and-local-Mac-OS-pasteboard/408374 # 408374](https://superuser.com/questions/407888/synchronize-pasteboard-between-remote-tmux-session-and-local-mac-os-pasteboard/408374#408374)

linux —从远程 SSH 会话获取本地剪贴板上的项目—堆栈溢出—[https://Stack Overflow . com/questions/1152362/Getting-Items-on-the-Local-Clipboard-from-a-Remote-SSH-Session](https://stackoverflow.com/questions/1152362/getting-items-on-the-local-clipboard-from-a-remote-ssh-session)

面向管理员的 systemd，部分 XI，inetd 服务—[http://0pointer.de/blog/projects/inetd.html](http://0pointer.de/blog/projects/inetd.html)

一个类似 inetd 的套接字激活服务的例子。# systemd # inetd # systemd . socket—[https://gist.github.com/drmalex07/28de61c95b8ba7e5017c](https://gist.github.com/drmalex07/28de61c95b8ba7e5017c)

Systemd socket 文件 stdin 重定向/系统管理/ Arch Linux 论坛—【https://bbs.archlinux.org/viewtopic.php?id=207834 

samoshkin/tmux-config: tmux 配置，为您的 Tmux 增压，打造舒适凉爽的终端环境—【https://github.com/samoshkin/tmux-config 