# 具有 rails gem 祖先的嵌套类别。

> 原文：<https://medium.com/hackernoon/nested-categories-with-rails-gem-ancestry-112ca8bbbf98>

[想象一下](https://hackernoon.com/tagged/imagine)你正在处理[项目](https://hackernoon.com/tagged/project)其中你有类别，以及这些类别中的产品，以服装网站为例。您可能有以下类别树:

```
Women
  - Bags
  - Clothes
    - Shirts
    - Jeans [SELECTED]
      - Straight
      - Skinny
  ...
Men
  - Accessories
  - Shoes
  ...
```

数据库结构非常简单——id、parent_id、类别标题和产品类别 id。非常简单，您可以很容易地获得某个类别的父类和/或子类。

但是你需要考虑——如果你选择了牛仔裤，就意味着你想要牛仔裤+直筒牛仔裤+紧身牛仔裤等等。要实现这一点，您需要选定的类别 id 及其子类别 id。用 rails 很容易得到它们，只需为它编写一个小的自定义范围。

但是如果孩子有自己的孩子，树深得多呢？您将不得不编写高级查询、连接、递归来获取所有级别上的子 id。或者假设您已经选择了最后一个类别，并且您想要获取它的所有现有父类别，或者您想要所选类别的兄弟类别(所选级别上的所有项目，如衬衫、牛仔裤等)。通过为所有这样的情况编写定制的范围和方法，您将很快得到一个庞大而混乱的模型。

处理嵌套类别的最好工具之一是 gem Ancestry([https://github.com/stefankroes/ancestry](https://github.com/stefankroes/ancestry))，它有很多助手作用域和方法，特别是对于上面提到的情况。

## **用途**

祖先非常容易理解和使用。

此处举例项目:[https://github.com/gioch/ancestry_test.git](https://github.com/gioch/ancestry_test.git)

*   将 gem `ancestry`添加到 Gemfile。
*   为类别创建模型(没有 parent_id 或类似东西，ancestry 会为我们处理它)
*   生成迁移以将祖先字段添加到类别:

```
rails g migration add_ancestry_to_category ancestry:string:index### it will add new column to categories, which will handle parent/child stuff
```

*   将“has_ancestry”添加到类别模型

```
class Category < ApplicationRecord
  has_ancestry
end
```

## 创建类别和子类别

创建子类别很容易，你只需要在创建时指定`parent`:

```
women = Category.create(name: 'Women')
women_bags = Category.create(name: 'Bags', parent: women)
women_clothes = Category.create(name: 'Clothes', parent: women)# Check seeds.rb inside example project for more seeds.
```

## 获取所选类别的所有子类别

祖先的助手们正在像魔法一样工作。使用方法`children`获得子类别，或者使用`child_ids`只获得子类别的 id 数组:

```
# category {Women}
cat = Category.first# [{Bags}, {Clothes}]
child_objects = cat.children# [2, 3]
child_ids = cat.child_ids
```

但是，如上所述，如果树非常深，并且您想要所有级别的子节点，请使用`descendants`来代替:

```
# [{Bags}, {Clothes}, {Shirts}, {Jeans}, {Straight}, {Skinny}]
all_children = cat.descendants# [2, 3, 4, 5, 6, 7]
all_child_ids = cat.descendant_ids
```

## 获取所选类别的所有父类别

如果您选择了树中的最后一个类别，并且您想要获得所有父类别，请使用`ancestors`或`ancestor_ids`:

```
# get category {Straight}
cat = Category.find(7)# get parent category objects [{Women}, {Clothes}, {Jeans}]
all_parents = cat.ancestors# get parent category ids [1, 3, 5]
all_parent_ids = cat.ancestor_ids
```

## **获取指定类别的产品**

```
# get category {Clothes}
cat = Category.find(3)# get child ids
descendant_ids = cat.descendant_ids# include selected category id if needed
descendant_ids << cat.idProduct.where(category_id: descendant_ids)
```

祖先还有很多其他有用的方法。

## 额外资源

点击这里查看祖先的 github 页面-【https://github.com/stefankroes/ancestry 

查查 Railscast 的血统—【http://railscasts.com/episodes/262-trees-with-ancestry 