# 单元测试中你不知道自己需要的最好的新特性

> 原文：<https://medium.com/hackernoon/the-best-new-feature-in-unittest-you-didnt-know-you-need-e0d26c213dce>

为了更好的阅读体验，请在我的网站上查看[这篇文章。](https://hakibenita.com/the-best-new-feature-in-unittest-you-didnt-know-you-need)

我喜欢不时阅读我认为自己很了解的*模块的文档。python 文档不是一个令人愉快的读物，但有时你会有所收获。*

![](img/982fbc984b4fae3ae66e10401d1200f8.png)

Same thing but slightly different

# 区分测试迭代

让我们从一个简单的函数开始，检查一个数是否是偶数

```
def is_even(n):
    return n % 2 == 0
```

和一个简单的测试

```
class TestIsEven(TestCase):

    def test_should_be_even(self):
        self.assertTrue(is_even(2))
```

很好，让我们再添加一些案例:

```
class TestIsEven(TestCase):
    ... def test_zero_should_be_even(self):
        self.assertTrue(is_even(0)) def test_negative_should_be_even(self):
        self.assertTrue(is_even(-2))
```

这是一个简单的例子，我们复制了三次代码。让我们通过编写一个循环来迭代我们期望为偶数的值，来尝试做得更好:

```
class TestIsEven(TestCase): def test_should_all_be_even(self):
        for n in (2, 0, -2, 11):
            self.assertTrue(is_even(n))
```

这看起来越来越优雅了，那么**有什么问题吗？**我加了一个奇数值 11，测试失败。让我们运行测试，看看它是什么样子的:

```
F
===================================================
FAIL: test_should_all_be_even (__main__.TestIsEven)
— — —— — — — — — — — — — — — — — — — — — — — — — — Traceback (most recent call last):File “subtest.py”, line 18, in test_should_all_be_even
self.assertTrue(is_even(n))
AssertionError: False is not true
```

果然失败了，但是**哪个值失败了**？

# 输入子测试

在 python 3.4 中有一个新特性叫做 [**子测试**](https://docs.python.org/3.5/library/unittest.html#unittest.TestCase.subTest) 。让我们来看看它的实际应用:

```
class TestIsEven(TestCase): def test_should_all_be_even(self):
        for n in (0, 4, -2, 11):
            **with self.subTest(n=n):** self.assertTrue(is_even(n))
```

运行该测试会产生以下输出:

```
F
==========================================================
FAIL: test_should_all_be_even (__main__.TestIsEven) (**n=11**)
— — — — — — — — — — — — — — — — — — — — — — — — — — — — — Traceback (most recent call last):File “subtest.py”, line 23, in test_should_all_be_even
self.assertTrue(is_even(n))
AssertionError: False is not true
```

那么哪个值失败了呢？11!我 **t 在标题**里。

多重失败是什么样子的？

```
F
===========================================================
FAIL: test_should_all_be_even (__main__.TestIsEven) (**n=3**)
— — — — — — — — — — — — — — — — — — — — — — — — — — — — — Traceback (most recent call last):File “subtest.py”, line 23, in test_should_all_be_even
self.assertTrue(is_even(n))
AssertionError: False is not true==========================================================
FAIL: test_should_all_be_even (__main__.TestIsEven) (**n=5**)
— — — — — — — — — — — — — — — — — — — — — — — — — — — — — Traceback (most recent call last):File “subtest.py”, line 23, in test_should_all_be_even
self.assertTrue(is_even(n))
AssertionError: False is not true==========================================================
FAIL: test_should_all_be_even (__main__.TestIsEven) (**n=11**)
— — — — — — — — — — — — — — — — — — — — — — — — — — — — — Traceback (most recent call last):File “subtest.py”, line 23, in test_should_all_be_even
self.assertTrue(is_even(n))
AssertionError: False is not true
```

就好像我们写了三个独立的测试用例。

**盈利！**

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)