# Elm 中求和类型的迭代和解码

> 原文：<https://medium.com/hackernoon/on-iterating-and-decoding-sum-types-in-elm-2329736b9f29>

*实验性*

*感谢*[*imright _ andunknowit*](https://www.reddit.com/user/imright_anduknowit)*和*[*enumeratable _ any*](https://www.reddit.com/user/Enumerable_any)*[*Reddit*](https://www.reddit.com/r/elm/comments/68zxvy/on_iterating_and_decoding_sum_types_in_elm/)*对他们的建议。**

*在 Elm 中，您没有一种本地方式来列出 sum 类型中的所有标签。*

*假设我们有一个水果类型，定义为:*

```
*type Fruit
  = Orange
  | Lemon
  | Apple*
```

*如何创建一个包含所有水果(【橙子、柠檬、水果】)的列表而不遗漏一个？*

*首先，让我们创建一个函数 nextFruit，该函数给定一个水果，将返回一个可能的水果，表示总和中的下一个水果:*

```
*nextFruit : Maybe Fruit -> Maybe Fruit
nextFruit maybeCurrentFruit =
    case maybeCurrentFruit of
        Nothing ->
            Just Orange
        Just currentFruit ->
            case currentFruit of
                Orange ->
                  Just Lemon
                Lemon ->
                  Just Apple
                Apple ->
                  Nothing*
```

*接下来，让我们创建水果清单:*

```
*fruitList : List Fruit
fruitList =
  let
    buildFruitList : Maybe Fruit -> List Fruit -> List Fruit
    buildFruitList maybeCurrentFruit allFruits =
      case maybeCurrentFruit of
        Nothing ->
          allFruits
        Just currentFruit ->
          buildFruitList (nextFruit maybeCurrentFruit) (currentFruit :: allFruits)
  in
    buildFruitList (nextFruit Nothing) []*
```

*如果你运行水果清单，你会得到[苹果，柠檬，橘子]。*

*现在，如果你在 Fruit 中添加香蕉，你会得到一个编译器错误，说 nextFruit 中的 case 不处理香蕉的可能性。你必须重写它:*

```
*nextFruit : Maybe Fruit -> Maybe Fruit
nextFruit maybeCurrentFruit =
    case maybeCurrentFruit of
        Nothing ->
            Just Orange
        Just currentFruit ->
            case currentFruit of
                Orange ->
                  Just Lemon
                Lemon ->
                  Just Apple
                Apple ->
                  Just Banana
                Banana ->
                  Nothing*
```

*好吧，实话实说:你可以在编写 nextFruit 函数的时候出错，但是 1/，你会被警告一个新的标签已经被添加到类型和 2/，你可以从类型声明中生成这个代码。*

*最后一步是编写一个函数，给定一个字符串，它将返回一个可能的结果:*

```
*fruitFromString : String -> Maybe Fruit
fruitFromString fruitString =
  fruitList
    |> List.filter (\x -> toString x == fruitString)
    |> List.head*
```

*fruitFromString“Apple”将只返回苹果，fruitFromString“菠萝”将不返回任何内容。*

*你可以概括水果清单:*

```
*enumList : (Maybe a -> Maybe a) -> List a
enumList nextEnumFn =
  let
    buildEnumList : Maybe a -> List a -> List a
    buildEnumList maybeCurrentEnum allEnums =
      case maybeCurrentEnum of
        Nothing ->
          allEnums
        Just currentEnum ->
          buildEnumList (nextEnumFn maybeCurrentEnum) (currentEnum :: allEnums)
  in
    buildEnumList (nextEnumFn Nothing) []*
```

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*