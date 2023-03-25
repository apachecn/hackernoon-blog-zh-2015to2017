# NPM 作为 Python 项目的构建工具

> 原文：<https://medium.com/hackernoon/how-i-used-npm-to-setup-a-python-project-dac33f0c7997>

听起来很疯狂，不是吗？

不要误解我。Python 是一种强大的语言。我喜欢 python。但我总觉得 python 少了点什么。*就像你从未约会过的漂亮女孩*。

![](img/0897733cc2c3f311a063cc3e36f34753.png)

在我看来，

> 与使用 NodeJs 和 npm 的项目相比，为一个中型到大型的 Python 项目设置工作环境真的非常非常困难！！！

让我们通过几种方法来使用 npm 来帮助 python 解决这个问题。

# 1.执行脚本

每个 NodeJs 项目都有一个`**package.json**`文件。您可以在一个文件中定义从版本到依赖项到脚本再到自定义配置的所有内容。运行脚本非常简单，

```
npm run <script-name>
```

我将跳过 npm 的细节。你可以在 scotch.io 文章中读到更多关于 npm 的力量。

> 这与 Python 相比如何？

python 中设置项目的一个标准选项是 setuptools 包。基本的元数据配置，如名称、版本和作者都是无关紧要的。它还提供了许多复杂的选项，如`cmdclass`和`scripts`。但是即使是一个简单的动作，比如删除一些自动生成的文件，也是非常困难的。

比方说，在运行测试套件之后，您需要删除`coverage/`目录中的所有文件。在 python 中，如果您想自动化这项任务，您需要做几件事情。

1.  在`setup.py`(使用`setuptools`的地方)的`cmdclass`下增加一个条目
2.  编写一个新的 Python 类来扩展`distutils.command`基类
3.  编写一个 python 代码，该代码将使用`subprocess.Popen`来执行所需的命令。

与在`package.json`文件中添加一行条目`"clean_cov": "rm -rf coverage/*"`相比，这是一项繁重的工作。这里的另一个好处是，您可以将 npm 命令链接起来以耦合任务。

```
"clean_cov": "rm -rf coverage/*",
"test": "python setup.py test",
"test_and_clean": "npm run test && npm run clean_cov"
```

您还可以添加`pre`和`post`脚本来定义流程。这样，上面的代码可以写成:

```
"test": "python setup.py test",
"posttest": "rm -rf coverage/*",
```

另一方面，我对将一些构建命令集成到项目的源代码中感到不舒服。

# 2.配置 git 挂钩

我个人认为预提交和预推送 git 挂钩是 git 存储库的重要元素。这些无名英雄避免了对存储库的意外提交，这有助于用干净的提交维护干净的存储库。

在我正在做的项目中，我们计划有对应于以下检查的钩子。

1.  运行 pylint 检查并评估目标文件的 pylint 分数
2.  运行 pytest 并确保所有测试都通过
3.  避免提交来控制和开发分支
4.  确保没有被禁止的词语
5.  创建一个源代码发行版，并确保没有构建错误

那只是其中的 5 个，还有更多支票。显然，手动运行这些检查是不可取的。所以我需要配置钩子。

我找到了几个 pip 包，包括 [git-pylint-hook](https://github.com/sebdah/git-pylint-commit-hook) 、 [git-pre-push-hook](https://pypi.python.org/pypi/git-pre-push-hook) 和 [pre-commit](http://pre-commit.com/#python) ，它们似乎都做了我想要的事情。但是我能找到的库都没有合适的文档。定制插件就像一场噩梦。

> 我甚至觉得手动编写几个 shell 脚本比找到合适的 python 插件更容易。

现在让我们看看如何在有一个`package.json`文件的情况下实现这一点。

我为这个作业找到了这些[预提交](https://www.npmjs.com/package/pre-commit)和[预推送](https://www.npmjs.com/package/pre-push)库。因为我在包中已经有了运行上述检查的定制脚本，所以我所要做的就是安装库并将相关条目添加到`package.json`文件中。例如，我有如下配置的预推挂钩，

```
"pre-push": [
    "git-branch",
    "forbidden-words",
    "pylint",
    "test",
    "build"
]
```

这很简单，对吧？？？。我还应该在这里强调一下，`pre`和`post`脚本也可以在这里工作。然而，对于我们的项目，我们更喜欢显式地指定检查。

在配置这些钩子时，我发现了一个有趣的库，那就是 lint-staged 库。我建议你也看一看。

# 3.观看模式

为了解释我为什么需要这个特性，我应该首先提到我正在使用 intelliJ PyCharm 作为编写 python 代码的 IDE。pycharm 中缺少的一个特性是 IDE 中关于 pylint 问题的实时反馈，我希望在不久的将来能看到这个特性。比如 [eslint](https://eslint.org/) & [flow](https://github.com/facebook/flow) 与 Webstorm 无缝集成。

在他们将该特性添加到 pycharm 之前，我的最佳选择是将 pylint 配置为外部插件，并在更新 python 文件之前手动运行它。

然而，随着我的救世主`package.json`的出现，这变得容易多了。 [npm-watch](https://www.npmjs.com/package/npm-watch) 库(和其他类似的库)为我们提供了观察给定目录中特定文件类型的变化，然后执行 npm 脚本的选项。

例如，每当一个`.py`改变时，我们可以有一个如下的条目来运行 pylint。

```
"watch": {
  "pylint": {
    "patterns": [
      "app/**/*",
      "utils/**/*"
    ],
    "extensions": "py",
    "quiet": **true** }
}
```

# 最后的想法

虽然我真的很喜欢在 python 项目回购中拥有一个`package.json`文件，但我对这种方法的反馈褒贬不一。一些开发人员认为这种方法不是 pythonic 式的。

我个人并没有发现它不是**python 化**的问题，因为我相信工程师应该是[技术](https://hackernoon.com/tagged/technology)不可知论者，并且所有这些语言仅仅是完成一些工作的工具。尽管如此，为了尊重所有团队成员的需求，我在没有使用 npm 的情况下配置了本报告中提到的所有基本内容。我的下一篇文章可能会以那次经历为基础。

## 你觉得这种方法怎么样？

## 你有别的 pythonic 替代品吗？

## 请随意给我和所有其他读者一个评论！！！