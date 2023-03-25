# Python 函数，*args，**kwargs

> 原文：<https://medium.com/hackernoon/python-functions-args-kwargs-5d2d00f09c74>

![](img/687dc7b63b23ede01035b3e8d6750d93.png)

为什么在 python 中使用 args 和 kwargs？不久前，我还不理解夸尔格斯，我发现写点东西有助于建立对主题的理解，它允许信息被消化，就像橡皮鸭。另外，因为学习很有趣，填补知识空白更好，所以我们开始吧。[https://rubberduckdebugging.com/](https://rubberduckdebugging.com/)

我将从数字海洋教程中借用一些例子，因为他们解释的方式确实有助于我理解它。[https://www . digital ocean . com/community/tutorials/how-to-use-args-and-kwargs-in-python-3](https://www.digitalocean.com/community/tutorials/how-to-use-args-and-kwargs-in-python-3)

用 python 实现函数中的变量；为了将数据传递给函数，变量被设置并以特定的顺序传递。示例函数是用一个字符串和一个数字调用的，非常简单。

```
def profile_info(username, followers):
 print(“Username: “ + username)
 print(“Followers: “ + str(followers))profile_info("chris",15)>>Username: chris
>>Followers: 15
```

问题:这种方法和函数调用要求变量以特定的顺序传递，并且所有的变量必须总是被传递。
解决方案:添加一些缺省值，这样我们可以不传递或者传递一些值而不会出错。

```
def profile_info(username="name", followers=0):
 print(“Username: “ + username)
 print(“Followers: “ + str(followers))profile_info()
profile_info(username="thor")>>Username: name
>>Followers: 0>>Username: thor
>>Followers: 0
```

好处:通过关键字传递时顺序无关紧要。如果我们想混合和匹配变量的顺序，我们可以传递带有关键字的变量。

```
def profile_info(username="name", followers=0):
 print(“Username: “ + username)
 print(“Followers: “ + str(followers))profile_info(followers=6, username="thor")>>Username: thor
>>Followers: 6
```

很好，那么很多变量呢...使用*args，我们可以将数量未知的变量作为元组传递。元组和列表的区别是什么？在 python 中，元组是不可变的，而列表是可变的。

> 有些对象是可变的，这意味着它们可以被改变。其他的是不可改变的；它们不能被更改，而是在尝试更新时返回新的对象。[https://codehabitude . com/2013/12/24/python-objects-mutable-vs-immutable/](https://codehabitude.com/2013/12/24/python-objects-mutable-vs-immutable/)

为什么可变性很重要？

```
string_build = ""
for data in container:
    string_build += str(data)
```

很简单，但是因为字符串是不可变的对象，所以每次 concat 都会创建一个新的对象，丢弃旧的对象，结果会占用更多的内存。

解决方案:

```
builder_list = []
for data in container:
    builder_list.append(str(data))
“”.join(builder_list)

### Another way is to use a list comprehension
“”.join([str(data) for data in container])

### or use the map function
“”.join(map(str, container))
```

另一个陷阱是使用一个列表作为函数的参数，也就是众所周知的可怜的 args。

```
def my_function(param=[]):
 param.append(“thing”)
 return param

my_function() # returns [“thing”]
my_function() # returns [“thing”, “thing”]
```

尽管使用空列表作为默认值，python 只对定义求值一次，因此空列表只在第一次被调用之前为空。该函数将在每次调用时重用相同的列表。*哎呀

对于函数，返回到*args。列表很酷，但是对函数参数使用*args 选项非常有用。这样，函数可以接受任意数量的变量，而不必为每个排列重新构建调用。

```
**def** **multiply**(*args):
    z = 1
    **for** num **in** args:
        z *= num
    print(z)

multiply(4, 5)
multiply(10, 9)
```

这非常棒，但是它缺乏拥有不同的参数顺序和定义特定参数的能力。这给了我们在一个领域更多的灵活性，但在另一个领域却更少。对此有什么解决办法？

* *克瓦查

KWargs 或“关键字参数”允许您将带关键字的字典作为参数传递。因为字典几乎总是超级有用的。

```
**def** **print_values**(**kwargs):
    **for** key, value **in** kwargs.items():
        print("The value of {} is {}".format(key, value))

print_values(my_name="thor", your_name="hulk")
```

关键措辞的参数是一个相当奇妙的附加，然而，为了有效地利用它们，代码必须适当地处理字典。最后，如果目的是混合和匹配参数类型，声明的顺序需要像这样。

```
**def** **example2**(arg_1, arg_2, *args, kw_1="shark", kw_2="blobfish", **kwargs):
...
```

传给 args 和 kwargs 呢？很简单，只需建立一个列表或字典，并将其传递给适当的函数。

```
**def** **some_args**(arg_1, arg_2, arg_3):
    print("arg_1:", arg_1)
    print("arg_2:", arg_2)
    print("arg_3:", arg_3)

my_list = [2, 3]
some_args(1, *my_list)**def** **some_kwargs**(kwarg_1, kwarg_2, kwarg_3):
    print("kwarg_1:", kwarg_1)
    print("kwarg_2:", kwarg_2)
    print("kwarg_3:", kwarg_3)

kwargs = {"kwarg_1": "Val", "kwarg_2": "Harper", "kwarg_3": "Remy"}
some_kwargs(**kwargs)
```

结论，args 和 kwargs 在很多情况下都是很棒的，但是当然它们不是最终的解决方案。当编写函数和使用它们时，只使用需要的东西，不要只使用 kwargs，因为它是一个新的闪亮的玩具，因为当你只有一把锤子时，一切都是钉子。考虑优化，考虑内存使用，考虑可维护性和可读性，然后为工作使用合适的数据类型。

感谢 digital ocean 编写了一个关于 python 的精彩教程，请认真阅读他们关于 python 的系列文章。

[](https://www.digitalocean.com/community/tutorials/how-to-use-args-and-kwargs-in-python-3) [## 如何在 Python 3 | DigitalOcean 中使用*args 和**kwargs

### 在函数定义中，参数是指定给定函数可以接受的参数的命名实体。当…

www.digitalocean.com](https://www.digitalocean.com/community/tutorials/how-to-use-args-and-kwargs-in-python-3)