# 理解 Python 的下划线(_)

> 原文：<https://medium.com/hackernoon/understanding-the-underscore-of-python-309d1a029edc>

> 我的母语不是英语。抱歉，我的英语不好。请理解。

*下划线* (_)在 Python 中是特殊的。

虽然在大多数语言中,*下划线* (_)仅用于蛇形变量和函数(当然，不是全部),但它在 Python 中有特殊的含义。如果你是 python 程序员，`for _ in range(10)`，`__init__(self)` like 语法可能比较熟悉。

这篇文章将解释何时以及如何使用下划线*(_)并帮助你理解它。*

*在 Python 中使用下划线有 5 种情况。*

1.  *用于存储解释器中最后一个表达式的值。*
2.  *因为忽略了特定的值。(所谓“我不在乎”)*
3.  *赋予变量或函数的名称特殊的含义和功能。*
4.  *用作“国际化(i18n)”或“本地化(l10n)”功能。*
5.  *分隔数字文字值的位数。*

*让我们来看看每个案例。*

# ***用于解释器时***

*python 解释器将最后一个表达式值存储到名为“_”的特殊变量中。这个特性首先在标准的 CPython 解释器中使用，你也可以在其他 Python 解释器中使用它。*

```
*>>> 10 
10 
>>> _ 
10 
>>> _ * 3 
30 
>>> _ * 20 
600*
```

# ***用于忽略数值***

**下划线*也用于忽略特定值。如果不需要具体的值或者不使用这些值，只需将这些值赋给*下划线*即可。*

```
**# Ignore a value when unpacking* x, _, y = (1, 2, 3) *# x = 1, y = 3* *# Ignore the multiple values. It is called "Extended Unpacking" which is available in only Python 3.x*
x, *_, y = (1, 2, 3, 4, 5) *# x = 1, y = 5* *# Ignore the index* for _ in range(10):     
    do_something() *# Ignore a value of specific location*
for _, val in list_of_tuple:
    do_something()*
```

# ***赋予变量和函数名称特殊含义***

**下划线*可能最常用于“命名”。PEP8 是 Python 约定准则，它引入了以下 4 种命名情况。*

***_ single _ leading _ 下划线** 该约定用于声明模块中的 ***私有*** 变量、函数、方法和类。在`from module import *`任何与此有关的约定都被忽略。
但是，Python 当然不支持*真正的私有，所以我们不能强制一些东西私有，也可以直接从其他模块调用。所以有时候我们说它“内功指标弱”。**

```
**_internal_name = 'one_nodule' # private variable
_internal_version = '1.0' # private variable class _Base: # private class
    _hidden_factor = 2 # private variable def __init__(self, price):
        self._price = price def _double_price(self): # private method
        return self._price * self._hidden_factor def get_double_price(self):
        return self._double_price()** 
```

****single _ trailing _ underline _** 这个约定可以用来避免与 Python 关键字或内置的冲突。你可能不会经常使用它。**

```
**Tkinter.Toplevel(master, class_='ClassName') # Avoid conflict with 'class' keywordlist_ = List.objects.get(1) # Avoid conflict with 'list' built-in type**
```

****_ _ double _ leading _ 下划线** 这是关于语法而不是约定俗成的。*双下划线*将打乱一个类的属性名，以避免类之间的属性名冲突。(所谓的“mangling”是指编译器或解释器用一些规则修改变量或函数名，而不是按原样使用)
Python 的 mangling 规则是在用*双下划线声明的属性名前面加上“_ClassName”。* 也就是说，如果你在一个类中编写名为“_method”的方法，那么这个名字就会以“_ClassName__method”的形式出现错位。**

```
**class A:
    def _single_method(self):
        pass def __double_method(self): # for mangling
        passclass B(A):
    def __double_method(self): # for mangling
        pass**
```

**因为用*双下划线*命名的属性会像上面一样被破坏，所以我们不能用“ClassName”来访问它。_ _ 方法”。有时，有些人使用它就像真正的私人使用这些功能，但它不是私人的，也不建议这样做。更多详情，请阅读 [Python 命名](http://python.net/~goodger/projects/pycon/2007/idiomatic/handout.html#naming)。**

****_ _ double _ leading _ and _ trailing _ underline _ _** 此约定用于`__init__`、`__len__`等特殊变量或方法(所谓的“魔法”)。这些方法提供了特殊的语法特性或者做了特殊的事情。例如，`__file__`表示 Python 文件的位置，`__eq__`在执行`a == b`表达式时执行。
一个用户当然可以自定义特殊方法，这是很少见的情况，但是经常会修改一些内置的特殊方法。(例如，你应该用`__init__`初始化这个类，当一个类的实例被创建时，它将首先被执行。)**

```
**class A:
    def __init__(self, a): # use special method '__init__' for initializing
        self.a = a def __custom__(self): # custom special method. you might almost do not use it
        pass**
```

# **作为国际化(i18n)/本地化(l10n)功能**

**它只是约定俗成，没有任何语法功能。也就是说，下划线并不表示 **i18n/l10n** ，它只是一个将 **i18n/l10n** 绑定到*下划线*变量的约定，已经来自 C 约定。
用于 **i18n/l10n** 的内置库`gettext`使用了这个约定，支持 **i18n/l10n** 的 Python web 框架 Django 也引入并使用了这个约定。**

```
**# see official docs : [https://docs.python.org/3/library/gettext.html](https://docs.python.org/3/library/gettext.html)
import gettextgettext.bindtextdomain('myapplication','/path/to/my/language/directory')
gettext.textdomain('myapplication')
_ = gettext.gettext# ...print(_('This is a translatable string.'))**
```

# **分隔数字文字值的位数**

**这个特性是在 Python 3.6 中添加的。它用于使用下划线*和*分隔数字，以提高可读性。**

```
**dec_base = 1_000_000
bin_base = 0b_1111_0000
hex_base = 0x_1234_abcdprint(dec_base) # 1000000
print(bin_base) # 240
print(hex_base) # 305441741**
```

# **结论**

**到目前为止，我们已经讨论了 Python 的下划线。虽然我是一名 Python 程序员，但直到写了这篇文章，我才知道其中的一些。尤其是 **i18n/l10n** 对我来说非常陌生。
和我一样，我希望你从这篇文章中获得一些关于*下划线*的有用知识。**

**接下来，我将讲述更多关于 Python 的趣事。谢谢你。**

*****更新*** *新增新特性(PEP 515)在 Python 3.6 中新增***

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。**
> 
> **要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**