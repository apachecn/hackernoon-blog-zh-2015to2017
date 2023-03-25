# 编码面试:递归

> 原文：<https://medium.com/hackernoon/coding-interview-recursion-f0d60c9dbb60>

![](img/d803a4666d5ed271a06d5c16d40de2ab.png)

[Image from here](https://www.pexels.com/photo/books-school-stacked-closed-48126/)

## 如何通过递归来接近和思考

我最近开始寻找我的下一个机会(如果你喜欢你的工作场所，请联系我！)，并注意到很多面试问题都围绕递归展开。事实上，我最近面试的 12 家公司中有 4 家在面试过程中问了一个递归问题。

你可能遇到过一个面试问题，这个问题应该在你职业生涯中的某个时候递归解决。

虽然我现在对递归理解得更好了，但是当我三年前第一次学习它的时候，我还是很纠结。以下是我在准备找工作时，通过解决几十个递归问题收集到的一些技巧。

*在我们开始之前，本文假设对什么是递归有一个基本的理解。注意:任何可以递归求解的问题也可以迭代求解。然而，对于某些类型的问题，递归解决方案更清晰，可读性更好，也更容易实现。我在这里谈论我是如何用一种对我有意义的方式来思考递归的，希望你们中的一些人也会发现我的方法是有帮助的。*

## I .如何识别递归解决方案有意义的问题

简而言之，递归是一种通过将输入分解成越来越小的部分直到它不能再被分解而得到解决方案的方法。最终输出是通过将这些调用的输出与“较小的”输入放在一起计算的。

如果一个问题的输入由不相等的嵌套值组成，而输出需要遍历输入，这是需要递归的好迹象。

我见过的面试问题示例包括遍历嵌套散列，例如:

```
entertainment_materials = {
  romance: {
    comedy: {
      movies: ['50 first dates', 'eternal sunshine'],
      books: ["he's just not that into you :("],
      articles: ['How I automated dating with a Tinder Bot']
    }
  },
  action: {
    movies: ['LOTR', 'Transformers 1'],
    superhero: {
      movies: ['Wonder Woman'],
      books: ['Ironman comic', 'Batman comic'],
      batman: {
        movies: ['Lego Batman']
      },
      spiderman: {
        movies: ['Homecoming']
      }
    }
  }
  ...
}
```

或者嵌套数组，例如:

```
[7,[[1]], [1,[2,[3]]], 3, [4, 5], 9]
```

当然，这类问题绝非面面俱到。像二分搜索法、合并排序和树遍历这样的常见算法是用递归实现的。

为了确定递归是否是正确的方法，我问自己:

*“通过一遍又一遍地调用一个方法，对我的原始输入进行某种‘截断’，从而得到一个输出，这有意义吗？”*

和

*“这个原始输入能以一种合理的方式被‘截断’以达到一个输出吗？”*

在本文中，我将讨论一个使用嵌套散列作为输入的问题。假设我应该`find how many movies, books, and articles there are in the ‘entertainment_materials' directory`。同样，假设我们的解决方案应该是:`{books: 0, movies: 0, articles: 0}`的格式。

## 二。我如何写下我的解决方案

当我遇到递归时，我问自己:

1.  基本情况是什么？
2.  我如何到达基本情况？
3.  如何通过调用堆栈聚合数据？

## **1。基本情况是什么？**

我认为基本情况是这样的:

*“基本情况是当我们得到一个输入时，我们知道输出是什么。”*

因此，基本情况是终止递归的条件。

在这里的例子中，基本情况是输入中没有任何东西可供我们循环。这可能是因为输入为零(或空散列)。当这种情况发生时，我们知道输出是`{books: 0, movies: 0, articles: 0}`。

在我见过的所有涉及数组的递归问题中，基本情况是当`input_array.length <= 1`时。

因此，这个问题的解决方案可能会从以下内容开始:

```
def count_materials_by_type(directory)
  return { books: 0, movies: 0, articles: 0 } if directory.nil?end
```

## 2.我如何到达基本情况？

了解基本情况很重要。否则，我们会有无限递归。

在许多数组问题中，达到基本情况的方法要么是删除第一个元素，最后一个元素，要么是将数组分成两半。

在这个嵌套散列问题中，遍历每个键/值对是有意义的。如果值是另一个散列，我们需要用那个“更小”的散列进行递归调用。

```
def count_materials_by_type(directory)
  return { books: 0, movies: 0, articles: 0 } if directory.nil? directory.each do |key, value|
    if value.is_a?(Hash)
      count_materials_by_type(value) # ensures that input into the recursive call keeps "shrinking" until it gets to the base case
    end
  end
end
```

当然，除了返回目录本身，我们的方法不做任何事情。

在我们的例子中，每当我们在目录中遇到这些关键字时，我们需要增加书籍、电影和文章的数量。让我们调整方法，定义一个变量来跟踪这些信息

```
def count_materials_by_type(directory)
  result = { books: 0, movies: 0, articles: 0 } return result if directory.nil? || directory.empty? directory.each do |key, value|
    if key == :books || key == :movies || key == :articles
      result[key] += value.length
    end if value.is_a?(Hash)
      count_materials_by_type(value)
    end
  end result
end
```

这仍然不起作用，如果我运行它，我得到

```
{ books: 0, movies: 0, articles: 0 }
```

原因是因为我在每个调用堆栈中将结果重置为`{books: 0, movies: 0, articles: 0}`。因此，我实际上并没有跟踪或处理调用栈中的数据。

现在，我们开始讨论问题的实质。

## 3.如何跨调用栈 ***聚合数据？***

不幸的是，这个是通过练习得来的。然而，好消息是，你对这种类型的问题练习得越多，你就会建立越多的直觉，直到你知道为止。

从调用堆栈聚合输出的方式取决于问题。阅读[这篇文章](https://medium.freecodecamp.org/learn-recursion-in-10-minutes-e3262ac08a1)中的一些好例子。

在这个特殊的例子中，方法是将`result`散列传回到方法中。

```
def count_materials_by_type(directory, result = { movies: 0, books: 0, articles: 0})
  return result if directory.nil? directory.each do |key, value|
    if key == :books || key == :movies || key == :articles
      result[key] += value.length
    end if value.is_a?(Hash)
      count_materials_by_type(value, result)
    end
  end result
end
```

返回值是包含电影、书籍和文章的结果散列。每当我们在循环中遇到另一个 hash 值时，我们再次将那个较小的 hash 值传递给方法。在输入没有嵌套散列之前，我们不会命中返回值。每个调用堆栈返回的结果包含电影、书籍和文章的更新计数。

这是我对递归的思考过程:

1.  基本情况是什么？
2.  我如何到达基本情况？
3.  如何在调用堆栈中聚合数据？

同样，就像其他技能一样，熟能生巧。所以继续编码，去搞定那个面试吧！:)

# 如果你喜欢这个故事，给我一些掌声！