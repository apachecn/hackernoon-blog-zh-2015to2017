# 如何在 Mac OS X 上更改文件的最后修改日期和创建日期

> 原文：<https://medium.com/hackernoon/how-to-change-a-file-s-last-modified-and-creation-dates-on-mac-os-x-494f8f76cdf4>

这是我在 2009 年 5 月写的一个非常简单的关于如何在 Mac OS X 上改变文件的“修改日期”和“创建日期”属性的指南。从那时起，这些指示已针对 Mac OS X 10.10 Yosemite 进行了更新。

**日期格式键:**
YYYY —年份(可省略前两位/世纪)。
MM —一年中的月份，从 1 到 12。
DD —一个月中的第几天，从 1 到 31。
hh —一天中的某个小时，从 0 到 23。
mm——表示分钟的数字，从 0 到 59。

**如何更改文件的“修改日期”属性？**
1。打开终端应用程序(*/Applications/Utilities/Terminal . app*)，或者如果您喜欢使用 [iTerm](http://iterm.sourceforge.net/) 。

2.将此输入终端(不要按回车键),用所需的日期信息替换 *YYYYMMDDhhmm* :

```
touch -mt YYYYMMDDhhmm
```

3.打开 Finder 窗口，找到您想要修改的文件，并将其拖放到终端窗口中。这里有一个例子，说明此时应该在终端中输入什么:

```
touch -mt 200801120000 /Volumes/Mac\ HD/Pictures/somefile.jpg
```

*上例中的 200801120000* 代表“2008 年 1 月 12 日上午 12:00”或我的二十一岁生日。确保将 *200801120000* 更改为您想要的日期，并将*/Volumes/Mac \ HD/Pictures/some file . jpg*替换为您想要更改日期的文件的正确路径。

4.如果一切正常*，按下返回键*。

如何更改文件的“创建日期”属性？
*警告:这将改变“修改日期”和“创建日期”属性。*

1.打开终端应用程序(*/Applications/Utilities/Terminal . app*)，或者如果您喜欢使用 [iTerm](http://iterm.sourceforge.net/) 。

2.将此输入终端(不要按回车键),用所需的日期信息替换 *YYYYMMDDhhmm* :

```
touch -t 201212211111
```

3.打开 Finder 窗口，找到您想要修改的文件，并将其拖放到终端窗口中。这里有一个例子，说明此时应该在终端中输入什么:

```
touch -t 201212211111 /Volumes/Mac\ HD/Pictures/somefile.jpg
```

上面例子中的 201212211111 代表“2012 年 12 月 21 日上午 11 点 11 分”或组成玛雅神圣的卓克年历中“大循环”的 5125 年的结束(你可以放松，世界并没有在那时结束——当我写这篇文章时，我并不认为它会结束！).确保将 *201212211111* 更改为您想要的日期，并将*/Volumes/Mac \ HD/Pictures/some file . jpg*替换为您想要更改日期的文件的正确路径。

4.如果一切正常*，按下返回键*。

*以下是关于如何使用上述命令批量修改/更新/更改文件的补充信息。*

**如何批量更新多个文件的“修改日期”属性？** 1。确保所有要修改的文件都在同一文件夹的顶层目录中。如果不想修改文件，请不要将文件存储在此文件夹中。

2.打开终端应用程序(*/应用程序/实用程序/终端. app* )或者如果你喜欢使用 [iTerm](http://iterm.sourceforge.net/) 。

3.将此输入终端(不要按回车键),用所需的日期信息替换 *YYYYMMDDhhmm* :

```
touch -mt YYYYMMDDhhmm
```

4.打开 Finder 窗口，找到包含您想要修改的文件的文件夹，并将其拖放到终端窗口中。这里有一个例子，说明此时应该在终端中输入什么:

```
touch -mt 200801120000 /Volumes/Mac\ HD/Pictures/Album
```

5.在命令末尾添加 */** 。如果您只针对特定的文件类型，请包括其扩展名(例如 */*)。jpg* 或 */*。png* )。您的命令应该如下所示:

```
touch -mt 200801120000 /Volumes/Mac\ HD/Pictures/Album**/***
```

6.如果一切正常*按下返回键*。

**如何批量更新多个档案的“创建日期”属性？**
*警告:这将改变“修改日期”和“创建日期”属性。*

1.确保所有要修改的文件都在同一文件夹的顶层目录中。如果不想修改文件，请不要将文件存储在此文件夹中。

2.打开终端应用程序(*/Applications/Utilities/Terminal . app*)，或者如果您喜欢使用 [iTerm](http://iterm.sourceforge.net/) 。

3.将此输入终端(不要按回车键),用所需的日期信息替换 *YYYYMMDDhhmm* :

```
touch -t 201212211111
```

4.打开 Finder 窗口，找到包含您想要修改的文件的文件夹，并将其拖放到终端窗口中。这里有一个例子，说明此时应该在终端中输入什么:

```
touch -t 201212211111 /Volumes/Mac\ HD/Pictures/Album
```

上例中的 *201212211111* 表示“2012 年 12 月 21 日上午 11:11”。确保将 *201212211111* 更改为您想要的日期，并将*/Volumes/Mac \ HD/Pictures/Album*替换为包含您想要更改日期的文件的文件夹的正确路径。

5.在命令末尾添加 */** 。如果您只针对特定的文件类型，请包括其扩展名(例如 */*)。jpg* 或 */*。png* )。您的命令应该如下所示:

```
touch -t 201212211111 /Volumes/Mac\ HD/Pictures/Album**/***
```

6.如果一切正常*按下返回键*。

希望你喜欢或者至少觉得这篇教程有用。如果您有任何问题或意见，请告诉我！

*最初发表于 2009 年 5 月 31 日 http://danilo.ariadoss.com*[](http://danilo.ariadoss.com)**。**

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)*