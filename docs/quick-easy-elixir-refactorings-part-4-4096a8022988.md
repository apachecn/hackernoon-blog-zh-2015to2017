# 快速简单的灵丹妙药重构—第 4 部分

> 原文：<https://medium.com/hackernoon/quick-easy-elixir-refactorings-part-4-4096a8022988>

## 嵌套条件句

![](img/05c2abf945219e3f34063fdf06591b64.png)

在前一部分中，我们研究了如何通过传入一个函数来重构函数中间的条件。在此之前，我们看了在函数的开头和结尾重构 T4 条件句。

如果你还没有通读这个系列，我建议你从头开始

[第 1 部分—以条件](/@efexen/quick-easy-elixir-refactorings-part-1-17376e9c455a) [开头的函数第 2 部分—以条件](/@efexen/quick-easy-elixir-refactorings-part-2-4cd66bad1b20)
[结尾的函数第 3 部分—中间的条件](/@efexen/quick-easy-elixir-refactorings-part-3-def1aae4b072)

在这篇文章中，我们将看看如何处理嵌套条件句。有人认为我不喜欢条件句，这是可以理解的😉

## 嵌套条件句

嵌套条件在其他语言中是一个大得多的问题，通常是因为函数太长，应该已经被拆分了，但有时会出现这种情况，最好有一种简洁的方法来处理它们。

一个例子可能是这样的

```
defmodule Example do def authorised?(user) do
    case UserAuthenticator.authorised?(user) do
      {:ok, _user} ->
        true
      {:error, _user} ->
        case LegacyUserAuthenticator.authorised?(user) do
          {:ok, _user} ->
            true
          {:error, _user} ->
            false
        end
    end
  endend
```

这段代码并不十分优雅，阅读起来也有点棘手。想象一下，如果我们不返回简单的布尔值，而是执行一些更长的逻辑😓

我们可以像这样拆分`authorised?/1`函数来消除嵌套

```
def authorised?(user) do
  case UserAuthenticator.authorised?(user) do
    {:ok, _user} ->
      true
    {:error, _} ->
      legacy_authorized?(user)
  end
enddefp legacy_authorized?(user) do
  case LegacyAuthenticator.authorised?(user) do
    {:ok, _user} ->
      true
    {:error, _} ->
      false
  end
end
```

万岁，不再筑巢🙌通过使用本系列前面的技术，我们可以完全消除条件句，得到类似这样的结果

```
def authorised?(user) do
  user
  |> UserAuthenticator.authorised?()
  |> check_authorised()
enddef check_authorised({:ok, _}), do: true
def check_authorised({:error, user}) do
  user
  |> LegacyAuthenticator.authorised?()
  |> check_legacy()
enddef check_legacy({:ok, _}), do: true
def check_legacy(_), do: false
```

当你在初始条件的两个分支中都有嵌套的时候，这很好，因为它允许你把所有的分支都放在很多小函数上。

幸运的是，在我们的例子中，我们只有一个分支中的嵌套条件，这允许我们使用`with`语句来查看替代选项

```
def authorised?(user) do
  with {:error, _} <- UserAuthenticator.authorised?(user),
       {:error, _} <- LegacyAuthenticator.authorised?(user) do
         false
       else
         {:ok, _} -> true
       end
  end
```

😮 😍

从技术上来说，我们最终得到的仍然是一个条件，但是与我们上面的选择相比，我认为这是一个非常好的解决初始问题的方案。

如果你不熟悉`with`语句，我强烈建议你尝试一下，因为它可以让代码更容易推理，而且肯定比一堆嵌套的条件语句更清晰。

请点击掌声图标👏如果你喜欢这篇文章，请在这里或 Twitter 上关注我， [@efexen](https://twitter.com/efexen) 来寻找简短的可操作的文章，在那里我们会看到更多简单的技巧来提升你的[药剂](https://hackernoon.com/tagged/elixir)代码👍