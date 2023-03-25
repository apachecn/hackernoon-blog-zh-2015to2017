# 函数式编程如何毁了我

> 原文：<https://medium.com/hackernoon/how-functional-programming-ruined-me-7886c12a46fd>

大约 20 年前，我的第一门编程语言是 C 语言。我 10 岁时得到了 Borland C 编译器和《傻瓜 C 语言》这本书。我妈妈是一名地球物理学家，当时用 FORTRAN 工作(别担心，她现在用的是 Python)。她认为这将是一个良好的开端。当时真的没有很多众所周知的选择——如果你不知道要找什么或者不知道有人知道，那就不存在。无论如何，我努力奋斗过——对于一个 10 岁的孩子来说，C 语言是一门难学的语言。我记得我花了几年时间钻研它，通过最基本的涉及指针的例子来培养我的方法。我想说的是…我并不是一开始就是一个函数式编程神童，我在 C 语言中解决问题，热爱`for`循环。

足够的回忆…我们也许应该澄清一些事情。编程是指示计算机对*事物*执行*动作*。例如，您可能想要“大写列表中的每个单词”。指示计算机执行此操作的方式可能如下:

```
set i = 1
set l = length(list)       # call the 'length' functionloop:
  list[i] = capitalize(list[i])     # list[i] means get an item
  i = i + 1 
  goto(loop) if i < l 
```

在这个例子中，*事物*是:

1.  `i`:我们在列表中的位置
2.  `list`:名单本身
3.  代码中的一个地方，我们可以去那里

而*动作*是:

1.  `length`:告诉你一个列表有多长
2.  `capitalize`:大写一个单词
3.  `goto`:移动到一个位置

假设我们想把列表中的每个单词都变成小写……我们必须复制上面的大部分代码，然后用`downcase`替换`capitalize`。

那我们为什么要这么做呢？为什么我们不能做一个“动作”，只说“对列表中的每个元素执行这个动作”。我们可以！这就是函数式编程。根本性的飞跃(也是必需的语言特性)是开始把“动作”看作是另一种“事物”。一旦你能把一个动作传递给另一个动作，你就在进行功能性思考。

因此，在函数式语言中，我们得到了一个新的“动作”，称为`map`，它接受一个动作和一个列表:

```
set capitalized = map(capitalize,list)
set lowercased = map(downcase,list)
```

在这个例子中，Map 所做的是对`list`中的每一项执行`capitalize`或`downcase`。最基本的区别是，在函数式语言中，你只需读一下`map`，就能明白它所做的事情很简单，只需通过另一个函数运行一个列表中的每一项。简单明了。在 Javascript 这种函数式语言中，这两者看起来是这样的:

```
function upcase(string) {
  return string.toUpperCase();
}// Functional:var allUppercase = list.map(upcase);// Imperative:var allUppercase = [];
for(var i = 0 ; i < list.length ; i++) {
  allUppercase[i] = upcase(list[i]);
}
```

老实说，要读懂`for`的逻辑并不需要*，*太多，我知道在某个时候你会停止阅读每个单词，你只会看到它的代码(金发、黑发、红发……)。然而，对于更复杂的程序，将复杂的动作表示为简单动作的组合要好得多，唯一的方法是将*动作*视为另一种*事物。我已经习惯了这种思考方式，如果不被允许，我会立刻倒胃口。*

在以这种方式编写代码几年后，每当我不得不处理不是以这种方式构建的代码库时，我都会想念它。当我忙于揭开一个 2 深度 for 循环的秘密时，我的一部分希望我从未学过函数式编程。非常小的一部分…但是它在那里。