# PHPUnit 中用于快照测试的包

> 原文：<https://medium.com/hackernoon/a-package-for-snapshot-testing-in-phpunit-2e4558c07fe3>

## 一种无需编写实际测试用例的测试方法

![](img/85a803f78ac810e2e8afe33108662c6d.png)

快照测试的要点是断言一组数据与先前版本相比没有变化，这是数据的*快照*，以防止回归。经典的`assertEquals`和`assertMatchesSnapshot`之间的区别在于，在快照测试时，您不需要自己编写预期。当快照断言第一次发生时，它会创建一个包含实际输出的快照文件，并将测试标记为未完成。每次后续运行都会将输出与现有快照文件进行比较，以检查回归情况。

快照测试是最有用的大型数据集，可以随着时间的推移而改变，比如为 XML 导出或 JSON API 端点序列化一个对象。

我们的包公开了一个特性，为您的测试添加快照测试功能，可以通过 [composer](https://packagist.org/packages/spatie/phpunit-snapshot-assertions) 安装，并在 [GitHub](https://github.com/spatie/phpunit-snapshot-assertions) 上提供。还有一个例子

*我找不到快照测试的任何正式来源。我发现的最古老的库是脸书写的用来快照测试 iOS 用户界面的库。*[*Jest*](https://facebook.github.io/jest/)*——一个同样由脸书开发的 JavaScript 测试框架——最近流行的快照测试，因为它为测试用 React 这样的虚拟 dom 库构建的用户界面提供了一个优秀的工作流程。*

## 基本示例

让我们为一个简单的字符串“foo”做一个快照断言。

```
public function test_it_is_foo() {
    $this->assertMatchesSnapshot('foo');
}
```

断言第一次运行时，没有与字符串进行比较的快照。测试运行程序生成新的快照，并将测试标记为未完成。

```
> ./vendor/bin/phpunit

There was 1 incomplete test:

1) ExampleTest::test_it_matches_a_string
Snapshot created for ExampleTest__test_it_matches_a_string__1

OK, but incomplete, skipped, or risky tests!
Tests: 1, Assertions: 0, Incomplete: 1.
```

快照 id 是基于测试和测试用例的名称生成的。基本快照返回实际值的一个`var_export`。

```
<?php return 'foo';
```

让我们重新进行测试。测试运行人员将会看到断言已经有了一个快照，并进行比较。

```
> ./vendor/bin/phpunit

OK (1 test, 1 assertion)
```

如果我们将实际值更改为“bar”，测试将会失败，因为快照仍然返回“foo”。

```
public function test_it_is_foo() {
    $this->assertMatchesSnapshot('bar');
}
> ./vendor/bin/phpunit

1) ExampleTest::test_it_matches_a_string
Failed asserting that two strings are equal.
--- Expected
+++ Actual
@@ @@
-'foo'
+'bar'

FAILURES!
Tests: 1, Assertions: 1, Failures: 1.
```

当我们期望一个改变的值时，我们需要告诉测试运行者更新现有的快照，而不是让测试失败。这可以通过给`phpunit`命令添加一个`-d --update-snapshots`标志来实现。

```
> ./vendor/bin/phpunit -d --update-snapshots

1) ExampleTest::test_it_matches_a_string
Snapshot updated for ExampleTest__test_it_matches_a_string__1

OK, but incomplete, skipped, or risky tests!
Tests: 1, Assertions: 1, Incomplete: 1
```

因此，我们的快照文件包含“bar”而不是“foo”。

```
<?php return 'bar';
```

## 方法

断言是使用`assertMatchesSnapshot`方法完成的。

```
public function it_matches_something()
{
    $something = new Something();

    $this->assertMatchesSnapshot($something);
}
```

如果您正在处理 JSON 或 XML 数据，您最好使用专用的`assertMatchesJsonSnapshot`或`assertMatchesXmlSnapshot`方法，它将快照保存为`.xml`文件的`.json`，并在快照不匹配时提供更好的差异。

```
public function it_matches_something_json()
{
    $something = new Something();

    $this->assertMatchesJsonSnapshot($something->toJson());
}
```

## 快照文件

默认情况下，快照存储在测试类的同一级别的一个`__snapshots__`目录中。

```
__snapshots__/
    ExampleTest__test_it_matches_a_string.php
ExampleTest.php
```

可以通过覆盖`getSnapshotId`和`getSnapshotDirectory`来更改快照 id 和快照目录的名称。查看自述文件以获得更详细的解释。

## 司机

驱动程序使包可扩展，如果没有`Driver`接口，快照断言将局限于 JSON、XML 和带有`var_export`的通用值。驱动程序处理快照数据的序列化和匹配。例如，如果您的应用程序会大量使用 YAML 文件，您可以编写一个`YamlDriver`来将快照保存为真实的 YAML 文件，并改进 PHPUnit 的 diff 输出。

可以通过将自定义驱动程序传递给`assertMatchesSnapshot`来应用它们。

```
public function it_matches_yaml()
{
    $order = new Order();

    $this->assertMatchesSnapshot(
        $order->toYaml(),
        new YamlDriver()
    );
}
```

如果你对编写自定义驱动程序的详细解释感兴趣，他们在 readme 中有一个专门的章节。

## 通往 v1.0 的道路

我们已经决定标记 1.0 版，因为我们已经在一些项目中使用这个包没有问题。缺少的功能可以在以后的版本中添加。

**清理未使用的快照(**[](https://github.com/spatie/phpunit-snapshot-assertions/issues/17)****)****

**目前，没有办法确定哪些快照没有使用，哪些可以删除。旧的快照需要手动删除，一个“清理”任务将是受欢迎的自动化。**

****无黑客更新标志(**[](https://github.com/spatie/phpunit-snapshot-assertions/issues/22)****)******

****需要在`-d`之后指定`--update-snapshots`标志，这意味着设置自定义 php.ini 值。 [PHPUnit](https://hackernoon.com/tagged/phpunit) 不支持自定义 CLI 选项，但它可能会在[未来的](https://hackernoon.com/tagged/future)版本中添加([sebastianbergmann/PHPUnit # 2271](https://github.com/sebastianbergmann/phpunit/issues/2271))****

****尽管没有一个稳定的版本号，但很可能不会再有任何大的突破性变化了。****

****感谢 [@AlexVanderbist](https://twitter.com/alexvanderbist) 帮助完成这个包的集成测试！****

*   ****[GitHub 上的 spatie/phpunit-snapshot-断言](https://github.com/spatie/phpunit-snapshot-assertions)****
*   ****[spatie/phpunit-snapshot-Packagist 上的断言](https://packagist.org/packages/spatie/phpunit-snapshot-assertions)****
*   ****可以在我们的网站上[找到我们开源包的概述](https://spatie.be/en/opensource)****

****[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)********[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)********[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)****

> ****[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。****
> 
> ****如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！****

****![](img/be0ca55ba73a573dce11effb2ee80d56.png)****