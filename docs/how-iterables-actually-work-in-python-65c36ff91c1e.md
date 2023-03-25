# Python 中 Iterables 的实际工作方式

> 原文：<https://medium.com/hackernoon/how-iterables-actually-work-in-python-65c36ff91c1e>

Python 语言最令人印象深刻的特性之一是“for-each”循环结构的使用，自从我开始使用 Python 以来，这种循环结构一直令我敬畏。对于外行来说，这里有一个简单的 *for* 循环，它打印前 10 个自然数:

```
for num in range(1, 11):
    print(num)
```

我们还可以以类似的方式循环遍历原始类型，如*列表、元组、字典*和*字符串*:

```
numbers = [1, 2, 3, 4, 5]
record = ('Kshitij', 21, 'Loves Python')
details = {
        'name': 'Kshitij',
        'age': 21
       }for num in numbers:
    print(num) # 1 2 3 4 5for data in record:
    print(data) # Kshitij 21 Loves Pythonfor key, value in details.items():
    print(key, value) # age 21 name Kshitij
```

当一个人使用`class`在 Python 中实现很少的数据结构时，他感觉到了对存储在其实例中的数据进行循环的欲望。这就是迭代器协议发挥作用的地方。

## 示例实现

让我们假设我们的任务是实现一个[标准的 52 副牌](https://en.wikipedia.org/wiki/Standard_52-card_deck)。一个示例实现可能如下所示:

这对于创建新的`Deck`实例并表示它很有效。然而，这个实现中的一个主要难点是缺乏迭代`Deck`对象的能力。

```
>>> from cards import Deck
>>> new_deck = Deck() # New deck instantiated
>>> print(new_deck)
... # Works great
>>> for card in new_deck:
...     print(card)TypeError: 'Deck' object is not iterable
```

聪明的人可以探究实例`new_deck`，并得出结论`cards`属性保存了迭代所需的数据，事实上它是一个`list`。有了这些知识，他可以如下破解上面的循环:

```
>>> for card in new_deck.cards:
...     print(card)
Card(...)
..
..
```

这段代码非常好用。然而，最终用户必须获得关于执行迭代的实现的内部信息。这使得我们的代码失去了数据抽象的优势，并导致实现的许多期望。

> 一定有更好的办法！

在 Raymond Hettinger 的热情推动下，我寻找方法来改进我的实现，以便与 Python 的`for`循环相结合。

很快我就找到了答案——**迭代器协议。**

# 迭代器协议

为了学习什么是协议以及如何用 Python 实现它，我们需要理解一些基本术语。

## 可迭代的

*   它是任何你可以用 for 循环循环的对象。
*   可重复项不总是可索引的，不总是有长度的，也不总是有限的。
*   可以将一个 iterable 传递给`iter()`内置函数，为它们获取一个迭代器。

## 迭代程序

*   迭代器只有一个任务:返回 iterable 中的“下一个”项。
*   [迭代器](https://docs.python.org/3/glossary.html#term-iterator)可以传递给内置的`next`函数，从中获取下一项，如果没有下一项(因为我们到达了末尾)，将引发一个`StopIteration`异常。
*   迭代器在传递给内置的`iter()`时返回自身。

# 协议

## 步骤 01:`iter()`内置如何工作？

每当解释器需要迭代一个对象`x`，它会自动调用`iter(x)`。`iter`内置功能:

1.  检查对象是否实现了，`__iter__`方法，并调用它来获得一个*迭代器。*
2.  如果没有实现`__iter__`方法，但是实现了`__getitem__`方法，Python 会创建一个*迭代器*，尝试从索引`0`开始按顺序获取项目。
3.  如果失败，Python 会抛出`TypeError`异常，说`<classname> object is not iterable`。

## 步骤 02:如何实现协议？

我将介绍实现迭代器协议的两种方法:

## 方法 1:传统方式

1.  创建一个表示*迭代器*(比如 **DeckIterator** ) *的新类。*
2.  在 **DeckIterator 中实现以下两种方法:**

`__next__`:返回 iterable 中的下一项。

`__iter__`:返回自身，即`self`。

3.在您想要迭代其实例的类中定义一个`__iter__`方法，即类 **Deck。**该方法应该返回 **DeckIterator** 的一个实例。

## 方法 2:务实的方式

1.  将 **Deck** 类中的`__iter__`方法实现为*生成器函数*。

## 特征

这是所有特性的列表，只要我们实现了协议，我们的对象就会神奇地支持这些特性。

1.  通过 for 循环进行迭代
2.  类似于元组的解包
3.  可用于列表理解
4.  可用于消耗 iterable 的内置函数(如`min`、`max`)。

```
>>> new_deck = Deck()>>> # 1\. Looping through a for loop
>>> for card in new_deck:
...     print(card) # Works great!>>> # 2\. Unpacking similarly to tuples
>>> first_card, *rest, last_card = new_deck>>> # 3\. List Comprehensions
>>> spades = [card for card in new_deck if card.suit == 'Spades']>>> # 4\. Built-in functions
>>> max_card, min_card = max(new_deck), min(new_deck)
```

# 吸取的教训:

1.  Python 中的迭代器不是类型的问题，而是协议的问题，也就是说，任何实现这个协议的类都可以被迭代。
2.  Python *groks* 迭代。

希望迭代器协议的知识能在你写 Python 的时候帮到你。为了提高对 Python 这一看似不被重视的特性的认识，我提议在 PyCon India 2017 上做一个关于这个主题的[演讲。](https://in.pycon.org/cfp/2017/proposals/iterators-generators-and-you~e9OYb/)