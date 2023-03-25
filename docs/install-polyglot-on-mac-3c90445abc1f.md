# 在 Mac 上安装 PolyGlot

> 原文：<https://medium.com/hackernoon/install-polyglot-on-mac-3c90445abc1f>

hy PolyGlot？

我总是和一些[软件](https://hackernoon.com/tagged/software)有关系，因为一个好的软件[工程师](https://hackernoon.com/tagged/engineer)总是使用已经存在的库，而不是重新创建它们。

我再次开始使用 NLP，但这一次，我选择 Python 来完成我的任务，所以在使用了一些 **Githubbing** (我不知道这是否真的是一个单词，我已经创建了一个词典)PolyGlot 似乎是一个很好的库来完成大多数 NLP 任务。

这是安装指南(带错误处理)

*   尝试使用 pip 安装 polyglot

```
$ pip install polyglot
```

*   如果您看到类似下图的错误

![](img/33cae82daa56b710eae8324e38517aa7.png)

这意味着，您缺少 icu4c 库。

许多操作系统不提供对 Unicode 和符合标准的文本处理服务的完全支持，因为我们正在处理需要这些服务的 NLP 库。

*   首先让我们使用 brew 安装 icu4c 库。

```
$ brew install icu4c
```

*   使用检查安装

```
$ ls -l /usr/local/opt/icu4c/include/
```

现在安装 pyicu

```
$ CFLAGS=-I/usr/local/opt/icu4c/include LDFLAGS=-L/usr/local/opt/icu4c/lib pip install pyicu
```

现在最后的命令

```
$ pip install polyglot 
```

参考文献

http://polyglot.readthedocs.org/en/latest/Installation.html
https://github.com/Homebrew/homebrew/issues/34170

在 Twitter 上关注 [@pradyumna_d](http://twitter.com/pradyumna_d) 了解更多信息..