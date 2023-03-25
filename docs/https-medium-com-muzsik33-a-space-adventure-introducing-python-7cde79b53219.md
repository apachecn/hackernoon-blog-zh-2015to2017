# 介绍 Python 的太空冒险

> 原文：<https://medium.com/hackernoon/https-medium-com-muzsik33-a-space-adventure-introducing-python-7cde79b53219>

## 这篇文章不是针对绝对的初学者。这是专门为 JS 开发人员或类似的人准备的，他们正在寻找非常流行的编程语言 Python 的有趣介绍。解释了许多概念:多重继承，列表理解，语言的基础和特性。所有的代码都可以立即复制/粘贴到一个 repl 中，在文章的末尾有一个 repl 可以直接查看。

# **在 90328443435329598 个外星生物的宇宙中。两个新生命来自虚空…**

![](img/c8c52cdc8dcaf6de8d9a28c36e1f6605.png)

Python Alien

> 你好人类。我听说你叫马塞尔。而你是 7530305890 人类中的一员。
> 
> 我是一只[巨蟒](https://hackernoon.com/tagged/python)，我的名字叫圭多，是三只巨蟒之一。
> 
> 现在，有:90328443435329600 外星生物。

现在，一些代码，完全编译成上面写的:

classes, multiple inheritance, __init__, def, self, global

`total_aliens = 90328443435329598`。也就是说 Python 是动态类型的。

`class Alien(object):`和`class Human(Alien):`表示外星人类继承类对象，而人类继承外星人类。

def, __init__, self, and global

`def`简单来说就是如何创建一个函数。

`__init__`是当一个类的新实例被创建时自动运行的，它是初始化器。

`self`:类中的每个方法都需要第一个参数。不一定非得是`self`，但这是惯例。这就是 self 的目的，摘自对堆栈溢出的简明描述:

*   `instance_object.parent_class_method(arg)`内部转换为:`parent_class.parent_class_method(instance_object, arg)`。所以`self`将实例对象绑定到方法调用，类似于 JS 中的`this`，需要注意一些微妙之处。

最后，`global`:如上所述编写它引用了全局变量，因此它可以在函数范围内修改。

super and pass

`super`与人类继承的直接父类，Alien 交互。该参数将新实例的`self.being`设置为等于`human`。

在 Python 中，当你写一个空函数、一个空的`if/else`或任何类似的东西时，`pass`是必要的。如果没有`pass`会出现错误。

Instance of class, print, dot notation

如图所示，创建了一个新实例。`print`是一个人记录值的方式。

获取与类的实例相关联的值是通过点符号来完成的。

好了，接下来是功能性[编程](https://hackernoon.com/tagged/programming)和故事的继续。

*人类，我有六项任务你必须完成才能离开这个星球:*

离开这个星球，有一个生成器函数，它运行你解决问题的每一个实例。这为你的飞船家园积累了燃料。这是功能:

[Generator function](https://stackoverflow.com/questions/1756096/understanding-generators-in-python), there will be a longer explanation later

*首先我给你一个列表(* `*['str_1', 'str_2', …]*` *)，里面会有六个字符串。你必须检查每个字符串的第一个字母在数字上是什么(a 是 0，b 是 1，等等。)，然后检查这个索引是否存在于列表中，最后将那个字符串添加到一个新列表中。返回新列表。*

list, for in, append, index, len

这将返回`['damnation', 'fuggedaboutit', 'zoological', 'buttercup']`。

`[]`:在这种情况下，它是一个列表，而不是数组。它们略有不同。

*   主要区别:`array([3, 6, 9, 12])/3.0`(数组语法)返回`array([1, 2, 3, 4])`，而`[3, 6, 9, 12]/3.0`(列表语法)返回错误。

`for str in strs:`是 Python 中最常见的循环类型。它从第零个索引开始遍历每个索引的值。

`some_list.append(some_value)`将指定值推到列表末尾。

`some_list.index(some_value)`在列表中查找`some_value`并返回值所在的第一个索引。如果没有找到，`ValueError`异常发生。

你的燃油量是 17%。现在，一个不同的任务。另一个功能。第一个论点:一个单一的数字。第二个参数:一个字典( `*{‘some_key': 'some_value', …}*` *)，包含一个名为* `*a_list*` *的键，其值是一个充满字符串(* `*'a_list':[...]*` *)和分隔符(* `*'separator': ‘something'*` *)的列表。如是:* `*{‘a_list': […], ‘separator': ‘something'}*` *)。第一个参数指定使用列表中的多少个字符串。将用分隔符连接的每个字符串存储到一个变量或内存位置，并返回该值。*

range, dictionary

这返回:`‘Trump is terrible! Genghis Khan is terrible! That noisy person is terrible! ’`

`range(num)`是指定个数的迭代器。如果只有一个参数，它将从零开始迭代到数字减一。Range 可以接受三个参数，这类似于一个精简的 for 循环:`range(-10, -100, -30) => -10 -40 -70`，无论`for this_thing_is in range(...)`是什么，`=>`都是值。

`{...}`:字典是无序的键/值对或者关联数组。关键字必须是字符串，要通过括号符号访问值，还必须插入一个字符串值或变量名。

你现在的利率是 34%。接下来，我给出一个更令人困惑的挑战。首先，你不知道给出了多少个论点！第一组参数包含数字、值 None 或字符串。第二组包含以这种方式关联的键/值对: `*some_keyword=some_value*` *。* `*some_keyword*` *将是一个字符串。* `*some_value*` *将是一个数字。归还字典。一个键/值。密钥只能是我第一次给你的逆序字符串(参数)。该值必须是串联字符串，仅使用从零索引到指定为该值的数字的关键字中的字符串，所有字符串都是一个不带空格的字符串。*

tuples (reversed), enumerate, if/elif/else, is None, isinstance(arg, int)

这登出:`{‘big amazing animal’: ‘elephant’}`。

如果您在`reversed(args)`之前输出参数，您会看到参数是:`(‘animal’, ’amazing', None, 6, ‘big')`。这是一个元组。不可变对象的序列。元组是用括号写的，它们不能像列表或数组那样在特定索引处改变。

`enumerate`允许用户拥有自动计数器并同时访问数值。

`if/elif/else`就是如何写 if/else 语句。

`is None`是返回 true 或 false 值以检查`None`值的方式。它类似于`null`，但在 JS 中更类似于`undefined`，因为许多代码实例都可以返回这个值，比如当一个人通过一个更高阶的函数改变一个列表时，他不期望返回一些东西，或者当一个函数不返回任何东西时，它返回`None`。

`isinstance(arg, int)`用于检查 arg 是否是指定类的实例。`int`在这种情况下也可以这样表达:

*   `isinstance(marcel, Alien)`还真会。类、字符串、整型和所有你期望的都可以用这种方式检查。

*好，很好。你现在是 51%。现在，一个有趣的问题。给你一个矩阵。一个列表中有三个列表，每个列表里面有四个字符串。首先，创建一个新列表，将每个列表的每个索引关联起来，如下:(* `*[[[0][0], [1][0], [2][0]…],[[0][1], [1][1]…], ...]*` *)然后，将每个列表展平成一个字符串，最后将该列表连接成一个字符串。你可以把所有这些逻辑放入一个语句中…一个列表理解。*

This needs explanation…

这返回:`You must make it look this way. Very extremely, magically, important thing to do!`

第一:`[[row[i] for row in matrix] for i in range(4)]`。同样的事情:

new list => loop through rows => append values into new row => append row into new list

人们必须从最外层的括号/逻辑来思考。

所以:`[all_the_logic]`就是说:创建一个新的列表，这里所做的一切将决定列表最终的样子。

`[[some_inner_logic] for i in range(4)]`:这将指定有 4 行，无论`[row[i] for row in matrix]`在每个实例中的结果是什么，每行都将是什么。

`[row[i] for row in matrix]`表示使用范围的索引 i (0，1，2，3 ),因为在创建第一、第二、第三和第四行时它是常数。在每行创建过程中，都会出现这种情况:

*   循环遍历`matrix` = >中的每一行，将`row[i]`处的值推入这个新创建的列表= >将这个列表附加到最初创建的列表中。

此时，创建的内容如下:`[['You’, ‘must', ‘make'], ['it', ‘look', ‘this'], ['way.', ‘Very', ‘extremely,'], ['magically', ‘important', ‘to do!']]`。转置矩阵。

到:`[str for sublist in [transposed_matrix] for str in sublist]`

new list => individual string in row => append strings to new list

同样，从外向内的括号。

创建新列表:`[result_of_logic]`

从最左边开始读 for 循环:`sublist in [transposed_matrix]`就是说:从第一个开始，遍历矩阵中的每个列表。

然后读取下一个 for 循环:`for str in sublist`。这相当于说在这个`sublist`内循环通过每个`str`。这是现在可以添加到列表中的值。

最后看列表理解的开头:`str`。那就是追加到列表中的:`['current_str', 'second_str', ...]`。因此，在某种程度上，最左边的值被推入新创建的列表，直到循环结束。仿佛`append`自动出现。

至于:`''.join(final_list)`，`''`是字符串分隔符。

注意最左边的`for`循环是如何首先出现的，再往右是嵌套。

好了，现在你已经达到了 68%,只剩下两个了！这个我需要你创建一个工厂函数。将有五个功能要实现。 `*append*` *(推到列表末尾)、* `*extend*` *(将一个列表连接到另一个列表)、* `*insert*` *(将项目插入特定索引)、* `*remove*` *(删除指定项目的每个实例)、以及* `*pop*` *(弹出列表中的最后一个项目)。您不能使用高阶函数等价物，并且您创建的每个函数* ***都不能改变原始列表*** *。你有三到四个论点。*

1.  *一份名单*
2.  *字符串形式的函数名*
3.  *函数需要的一个参数，如果它需要四个参数，则索引(用于插入)*
4.  *一个条目(仅插入时需要)*

*好吧，祝你好运！*

slices, list concatenation

要知道，这些功能中的每一个都不是实现这一点的最佳方式。使用相关的高阶函数要快得多。另外，要知道除了返回弹出内容的`pop`之外，高阶函数都返回`None`。

`append`和`extend`:这简直就是 Python 的精妙之处。将一个列表添加到另一个列表会自动连接这两个列表。`li.append(item)`和`li.extend(other_li)`是正确的语法，但是这两个选项会改变原始列表，而上面写的选项不会。`extend`中的`other_li`虽然没有变异。

`insert`和`pop`在这种情况下都使用切片。我会关注`insert`。注意:`the_list[:idx]`:这意味着将列表切片到索引，但不包括它。`the_list[idx:]`:就是从索引开始切片，包括索引到列表的最后一个索引。

`remove`:简单的列表理解(他们是极快的操作 btw)。`[x for x in the_list if x != item]`:`new_list = []`=>`for x in the_list:`=>`if x != item:`=>`new_list.append(x)`or，但技术上不是`new_list += [x]`。但是，请注意，真正的`li.remove(item)`只会删除该项的第一次出现。此`remove`删除所有事件。

*非常好，进入下一个。你还有 85%的燃料，这是最后一次了！下一个有点奇怪，因为它是两个功能合二为一。我会给你一个广泛的关键字和论点分类。您的函数将被多次调用，调用的参数数量是随机的，或者没有参数。和/或多种关键字。我需要你把我给你的所有参数值存储在一个非全局列表中，把所有关键字值存储在一个非全局字典中。返回字典 as so:* `*{'the_list': [...], 'the_dictionary': {...}}*` *。每次调用该函数时，根据输入* ***，列表或字典都会发生变化，所有值都会附加到之前调用的值中。***

最后，最终调用列表作为第一个参数被解包，字典作为第二个参数被解包到另一个函数中。

mutating default arguments, unpacking a list, unpacking an object

`(*args, li=[], dic={}, **keywords)`:注意，在这两个调用之间有两个不是关键字或参数的参数，这是必需的。

但是真正奇怪的部分，为什么`li`和`dic`在每次函数调用过程中不断变异？Python 不会为每个函数调用创建每个参数的副本，而是保存一个对原始默认参数的引用，如果不小心的话，他们很容易陷入创建不纯函数的陷阱。

至于这个:`back_to_earth(*dictionary_and_list()['a_list],` :这就是如何将一个列表解包到一个函数中，这样列表中的每一项都成为一个单独的参数。用`*args`抓取数值。至于`**dictionary_and_list()['a_dictionary])`:这将字典值解包到函数参数中。在字典中有相关键的函数中的每个关键字将被赋予该值。

以及代码输出的内容:

> *好吧，你那里:100%！好了，你完了，是时候离开这个 Python 世界了。祝贺你取得的所有成就！再见！
> 前进
> 3
> 后退
> 2
> 到
> 1
> 大地！
> 0*

最后但同样重要的是，在每个函数完成后，我运行`liftoff.__next__()`。正如您在下面的代码中看到的。有一点要知道。如果再运行一次，就会出现错误。为什么？嗯，生成器函数只能读一次，不能再读了。所以当迭代结束时，不再有`.__next__()`，所有先前的值都被垃圾收集了。

所有代码都在一个地方:

和回复:

[](https://repl.it/@jerrymuzsik/pythonIntroductionArticle) [## @jerrymuzsik 的文章

### 文章中的所有代码都放在一个地方。回复它](https://repl.it/@jerrymuzsik/pythonIntroductionArticle) ![](img/7cdd2091df5cc2f3ef30b98e6422d5ed.png)

This is Guido, Python alien

其他一些需要注意的事项:

1.  文件名、变量名和函数名用下划线命名。
2.  缩进:约定为 4 个空格。
3.  CamelCase 用的不多，只在课堂上用。
4.  如果变量是 globalthenitisalllowercase(如果变量是全局的，那么它全部是小写的)。

> 感谢你的阅读。任何建议，想法，评论，想法，掌声，或其他任何你觉得与这篇文章有关的事情，都将不胜感激！