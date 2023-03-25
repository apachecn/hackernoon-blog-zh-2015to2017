# Ecto:通过 URL 参数的多个过滤器

> 原文：<https://medium.com/hackernoon/ecto-multiple-filters-via-url-parameters-fd0e4a8b5074>

![](img/97d68f478573a0272999563ce6aa65d7.png)

## 概观

昨天，在开发一个 RESTful 的 API 时，我遇到了一个棘手的问题。

> 如何根据 URL 中传递的参数过滤一个 [Ecto](https://hackernoon.com/tagged/ecto) 结果？

在这个小教程中，我们将构建一个可重用的模块来处理过滤。

## 情况

我们构建了一个 API 端点来获取所有用户的待办事项。`todos`表有一个`state`列，每个待办事项可以有一个状态`done`、`doing`或`pending`。

## 我们努力实现的目标

我们希望根据请求 URL 的参数过滤查询结果。例如，当用户调用此端点时，我们应该只包括具有正在进行或“完成”状态的 todo 项目:

```
[https://](https://https://my-cool-api.com/v1/todos?filter[state]=doing,done)my-awesome-api.io/v1/todos?filter[state]=doing,done
```

## 样板文件

通常，为了获得所有用户的待办事项，我们在上下文文件中有这样的内容:

Todos Context Boilerplate

我们在控制器中这样调用这个函数:

Todos Controller Boilerplate

## 解决办法

由于过滤器参数将在 URL 中传递，我们需要将`params`变量传递给上下文的`list_todos/2`函数。我们需要这样修改我们的控制器:

通过这样做，我们还需要在我们的上下文中调整我们的`list_todos`函数的 arity。我们现在还将准备使用过滤器模块的上下文，稍后我们将构建过滤器模块。

**NOTE**: FilterEx is the name of the module that we will building later.

在**第 7 行**中，我们只是检查过滤参数是否存在。如果存在，我们以列表形式返回*过滤器参数*，如果不存在，我们只返回一个空列表。

我们现在将构建处理过滤的 **FilterEx** 模块。

## 构建 FilterEx 模块

在构建这个模块时，我们将利用**Ecto.Query.dynamic/2**。如果你想了解更多，这里有[官方文档](https://hexdocs.pm/ecto/Ecto.Query.html#dynamic/2)。基本上，`Ecto.Query.dynamic/2`让我们一点一点地构建查询表达式，然后在主查询中插入它。

"… *稍后在我们的主查询中插入*

您可能想知道为什么我们不像这样在我们的上下文中添加对 FilterEx 的调用:`where([t], t.user_id == ^user.id and ^FilterEx.filter(states, :state))`

不过，有个问题。

> `dynamic`可以插在`where`、`having`或`join`的`on`的根上。

这就是为什么我们在第一个`where`子句后添加了 FilterEx 调用，而不仅仅是对其进行插值。必须将其插入子句的根。

现在让我们定义模块并导入必要的 Ecto 模块。

```
defmodule FilterEx do
  import Ecto.Query # ...
end
```

`FilterEx.filter/3`期望第一个参数是工作查询，第二个参数是过滤器列表，第三个参数是列名。

```
defmodule FilterEx do
  import Ecto.Query @spec filter(Ecto.Query.t, list, atom) :: Ecto.Query.t  
  def filter(query, [head | tail], fieldname) do
    ...
  end def filter(query, [], _), do: queryend
```

在`filter/3`函数中，我们将构建我们的初始动态查询，该查询将被传递给`filter_field/3`函数以进一步构建动态查询。然后，我们将`dynamic_query`插入到我们的主查询中，并将其返回给管道。

```
... def filter(query, [head | tail], fieldname) do
    dynamic_query = 
      dynamic([q], field(q, ^fieldname) == ^head)
      |> filter_field(tail, field_name) query |> where(^dynamic_query) 
  end...
```

我们将在我们的`filter_field/3`函数中使用递归方法。

```
... def filter_field(dynamic, [head | tail], fieldname) do
    dynamic([q], field(q, ^fieldname) == ^head or ^dynamic)
    |> filter_field(tail, fieldname)
  end...
```

我们现在已经完成了 FilterEx 模块的构建。

以下是完整的版本:

感谢您的阅读。如果你有任何问题，可以随时在 Twitter 上跟我说[@ Vince rug](https://twitter.com/VinceUrag)

学到了有价值的东西？你可以随时给我买杯咖啡。❤

在 Github 上与我联系:

[](https://github.com/vinceurag/) [## 文斯·乌拉格

### vinceurag 有 11 个可用的存储库。在 GitHub 上关注他们的代码。

github.com](https://github.com/vinceurag/)