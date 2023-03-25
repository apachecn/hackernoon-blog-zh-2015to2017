# 符号链接没有像预期的那样工作

> 原文：<https://medium.com/hackernoon/symbolic-links-did-not-work-as-expected-6a3af628da53>

## 手册页中的简单修复方法在 MAC 上并不存在

如果`source_file`包含一个笑脸，并且我想创建一个从`some_directory/`到这个文件的[符号](https://hackernoon.com/tagged/symbolic)链接，那么“创建一个符号链接”的第一个谷歌结果[告诉我](https://kb.iu.edu/d/abbe)

```
$ ln -s source_file some_directory/link
```

但是，仔细一看，好像哪里出了问题！

```
$ cat some_directory/link
cat: some_directory/link: No such file or directory
```

继续读下去做一点实验，看看操作系统在哪里**真的**在寻找我们的符号链接的来源。如果你想跳到最后也没关系，我已经包含了一个 [StackOverflow](https://hackernoon.com/tagged/stackoverflow) 链接，里面有一些有用的解决方案。

## 设置

首先，让我们设置一个包含 ascii 笑脸的虚拟`source_file`

```
$ cd
$ echo ":)" >> source_file
$ cat source_file
:)
```

我们的目标是创建一个到`source_file`的符号链接。我们希望能够在`$ cat some_directory/link`看到`source_file`的笑脸。

就像我们在上面看到的，按照第一个谷歌结果[上的说明](https://kb.iu.edu/d/abbe)来“创建一个符号链接”并不像我们预期的那样工作

```
$ mkdir some_directory
$ ln -s source_file some_directory/link
$ cat some_directory/link
cat: some_directory/link: No such file or directory
```

## 好，我们的链接指向哪里？

经过一番挖掘，我意识到操作系统可能正在搜索我的源文件**相对于**我的链接的最终位置！我通过在我的链接旁边创建一个新的`source_file`来测试这个假设，并给它一个皱眉头的表情

```
$ echo ":'(" >> some_directory/source_file
```

新的目录结构看起来像

```
source_file # :)
some_directory/
|-- link
|-- source_file # :(
```

而`some_directory/link`的确是皱着脸指着文件

```
$ cat some_directory/link
:'(
```

> `some_directory/link`不是指向`source_file`，而是指向`some_directory/source_file`

## 修复#1:递归符号链接

一旦我诊断出问题，就很容易在[手册页](http://man7.org/linux/man-pages/man1/ln.1.html)中找到`ln`的正确选项。

```
$ ln -sr source_file some_directory/other_link
$ cat some_directory/other_link
:)
```

但是等等！事情没有这么顺利。据我所知，`ln`在 mac 上没有`-r`或`--recursive`选项。

## 修复#2:绝对路径

在 mac 上，我可以为`source_file`使用绝对路径

```
$ ln -s ~/source_file some_directory/absolute_link
$ cat some_directory/absolute_link
:)
```

## 修复#3:打出相对链接

或者，另一个选择(我更喜欢——不是每个人都将他们的 git 存储库签出到本地开发机器上的同一个文件夹中)是使用相对于符号链接的源文件路径

```
$ ln -s ../source_file some_directory/other_link
$ cat some_directory/other_link
:)
```

## 结论又名 TL/DR

和往常一样，StackOverflow 有一些更好的方法来解决这个问题

[](https://stackoverflow.com/questions/221267/copying-symbolic-links-in-mac-os-x) [## 在 Mac OS X 中拷贝符号链接

### 加入 Stack Overflow，学习、分享知识，打造您的职业生涯。复制符号的最简单方法是什么…

stackoverflow.com](https://stackoverflow.com/questions/221267/copying-symbolic-links-in-mac-os-x)