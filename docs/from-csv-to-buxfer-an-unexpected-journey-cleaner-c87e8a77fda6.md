# 从 CSV 到巴克斯弗:一次意想不到的旅行——更干净

> 原文：<https://medium.com/hackernoon/from-csv-to-buxfer-an-unexpected-journey-cleaner-c87e8a77fda6>

## 第 2 部分—清理程序:一个关于如何编写清理原始数据的程序的故事

![](img/846eecc97d656cb8fb89978e3834478d.png)

# 序文

在[引言—第 1 部分](/@wilk/from-csv-to-buxfer-an-unexpected-journey-introduction-ba43ef768fe2)中，我已经定义了本项目的基础设施，绘制了整体图，并收集了所有的部分。
现在是时候写第一个程序了:清洁工！

Cleaner 要做的唯一一件事就是通过删除无用的数据和元数据，将原始的 CSV 文件解析为随时可用的 CSV 文件。
结果将由收集器([第 3 部分](/@wilk/from-csv-to-buxfer-an-unexpected-journey-collector-4dbde92c1e7b))使用。

## 旅行

在本文中，我将介绍这一旅程的第二部分:

1.  [第 1 部分:引言](/@wilk/from-csv-to-buxfer-an-unexpected-journey-introduction-ba43ef768fe2)
2.  第 2 部分(本部分):更清洁
3.  [第 3 部分:收集器](/@wilk/from-csv-to-buxfer-an-unexpected-journey-collector-4dbde92c1e7b)
4.  [第 4 部分:Goxfer](/@wilk/from-csv-to-buxfer-an-unexpected-journey-goxfer-88d8a14e8905)
5.  [第 5 部分:结论](/@wilk/from-csv-to-buxfer-an-unexpected-journey-conclusions-b1274aa9841e)

## 先安装！

Cleaner 将是一个 [Python](https://hackernoon.com/tagged/python) 程序，所以我需要首先设置码头工人容器！
正如我在上一篇文章中提到的，我不想在我的系统上安装任何东西，所以我要通过添加一个新的**命令**来更新 docker-composit . yml 文件中的 setup-python 服务，该文件在开始时将只被调用一次:

很好。
我定义安装服务只是为了让 Python 开发环境做好准备。
此外，我还告诉 docker-const 用这个 Python Dockerfile 构建 *data.python3:1* 图像(该图像将用于其他服务):

修复版本会让你的系统变得可预测和确定:**不要依赖最新版本。**

Python 容器只安装了 python 包管理器 [**pip**](https://pypi.python.org/pypi/pip) ，还有一个通用*入口点*用于传递来自外部的命令:

现在，让我们试试这个容器并安装一个新项目的依赖项: [**pymongo**](https://api.mongodb.com/python/current/) ！
耶，这将是 [MongoDB](https://hackernoon.com/tagged/mongodb) 的 Python 驱动程序。
为此，我需要进入安装容器并启动" *pip install* "和" *pip freeze* "命令，这样我就可以安装依赖项和 pip 定义**需求所需的每个文件。**

```
# first, build the image
$ docker-compose build# --rm will remove the container after its execution
$ docker-compose run --rm setup bash
```

然后，从容器内部:

```
# don't forget to initialize, so the project's dependencies will be stored locally
$ virtualenv .
$ source ./bin/activate# install and freeze
# no need to mark a specific pymongo version during the setup phase
$ pip install pymongo
$ pip freeze > requirements.txt
```

好了，设置完成了！
如果我需要在另一个系统上恢复环境，没问题，docker-compose 会为我做同样的事情:

```
$ docker-compose run --rm setup-python
```

# 清洁工:手放在上面！

最后，本文的核心。数据！耶！
这是我要编码的项目的第一部分:**清洁工**。
该工具必须从一个装满 CSV 文件的文件夹中读取，然后将清理后的文件写入另一个文件夹。
我需要从这个样本出发:

```
# this is a header row
*Date  Account Name Number Description  Notes  Memo Category Type Action Reconcile  To With Sym  From With Sym  To Num.  From Num.  To Rate/Price  From Rate/Price*# this is a data row
"04/06/2016","Abbigliamento","","maglietta","","","Sbilancio-EUR","T","","N","€ 5,00","","5,00","","",""# another data row, part of the previous one
"","","","","","","Abbigliamento","S","","N","€ 5,00","","5,00","","1,00",""# another data row, part of the previous one
"","","","","","","Sbilancio-EUR","S","","N","","-€ 5,00","","-5,00","","1,00"
```

对此:

```
# header row
Date,Account Name,Description,To Num.# just one data row
04/06/2016,Abbigliamento,maglietta,"5,00"
```

让我们用 Python 来做这个吧！
要做的第一件事是定义*源文件夹*(清洗程序获取原始 CSV 数据的地方)和*目标文件夹*(存储清洗后的数据的地方)。
让我们创建一个样本文件夹(源)和一个清理过的文件夹(目标)。
顺便说一下，我希望用户有机会指定自己的目录:因此，我将使用**环境变量**和上面列出的默认值，一个名为 *SOURCE_FOLDER* ，另一个名为 *CLEANED_FOLDER* 。
但是首先，我要在 docker-compose.yaml 中创建一个 **cleaner** 服务:

Cleaner 将使用与 *setup-python* 服务相同的 docker 映像，因为它实际上是相同的。
定义完环境变量后，我需要在容器中挂载整个根文件夹，这样当命令执行时，它会找到 **python/src/cleaner.py** 。
因此，让我们在 src 文件夹中添加一个空的 cleaner.py:

```
$ touch python/src/cleaner.py
```

现在，**清洁工应该做什么？**
我们来定义一下程序:

1.  定义 CSV **列所需的**索引(如“日期”、“描述”等)。)
2.  从环境中检索**源和目标文件夹**
3.  **恢复/创建**dest 文件夹(我想从之前的迭代中清理掉它)
4.  **从源文件夹中读取文件列表**
5.  对于每个原始文件，**在 dest 文件夹中创建一个新的清理文件**

**是时候写下来了！**

看着上面的样品(原始的和清洁的)，我可以说需要的色谱柱是:

*   *日期*(指数:0)
*   *账户名称*(索引:1)
*   *描述*(索引:3)
*   *去小水。*(指数:12)

我要把它们放在常量里。

然后，让我们用 **os.getenv** 从环境变量中获取 source 和 dest 文件夹:

恢复/创建过程可以依次定义为 rmdir 和 mkdir，用 **shutil.rmtree** 和 **os.makedirs** :

使用**列表目录**和**列表理解**阅读 CSV 原始文件非常容易:

现在，最重要的部分！
用 **csv.reader** 和 **csv.writer** 扫描每个 raw 文件，解析它并在 dest 文件夹内创建一个新的清理文件:

使用 csv.reader 打开并解析 CSV 文件。
然后，在 dest 文件夹中创建清理后的文件。
最后，解析 CSV 文件的每一行，只取那些 **DATE_COLUMN** 字段非空的行。

好了，清洁剂也可以使用了。
这就是全部源代码:

让我们试试:

```
$ docker-compose run --rm cleaner
```

当执行终止时，dest 文件夹已经被清除的数据填充，就像上面列出的预期示例一样。
**干得好！**

# 第 2 部分结束

现在，我有了一个更干净的程序，可以将原始的不可读数据转换成干净的新数据，随时可以使用。
我想产生这个中间步骤，因为我想检查清理后的数据是否可靠；事实上，下一个程序将是收集器，它将使用这些转换后的数据来完成工作。

如果你喜欢这篇文章，不要忘记分享它！
第三部:收藏家见！

## 扰流器

源代码已经可以在这里获得:[https://github.com/wilk/from-csv-to-buxfer](https://github.com/wilk/from-csv-to-buxfer)