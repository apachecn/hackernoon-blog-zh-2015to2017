# 围棋中的 yes 有多快？

> 原文：<https://medium.com/hackernoon/how-fast-is-yes-in-go-1acc57b98815>

![](img/8cdb426f8455a004ffcb6671e12e32c7.png)

今天早上我在 Hackernews 上看到了这个[链接](https://www.reddit.com/r/unix/comments/6gxduc/how_is_gnu_yes_so_fast/?st=j3v3iw3c&sh=5651ea3c)。
这是一个关于使用 yes 命令从内存中写入速度的小实验。

```
$ yes
y
y
y
y
y
y
y
y
y
```

这是命令的全部内容。它打印出字母 y，永远。

从本文中，我们可以使用 pv 命令来测试用于写入/dev/null 的带宽。从 pv 手册页..