# recycler view plus sign kot Lin power

> 原文：<https://medium.com/hackernoon/recyclerview-plus-assign-kotlin-power-6f93ce980154>

![](img/2982c23053d4bb8f43ef36edd15c18f3.png)

用 recyclerview 创建列表在几乎每个项目中都很常见，这需要我们为每个列表创建适配器和视图容器。在某些情况下，当我们的应用程序中有许多列表时，我们会重复这个过程太多次。

在本文中，我们使用 Kotlin 特性以更简单快捷的方式创建简单列表。

让我们从介绍我们将得到的结果开始。

```
recyclerView.***setUp***(users, R.layout.*item_layout*, **{** nameText.*text* = **it**.**name** surNameText.*text* = **it**.**surname
}**)
```

当然，对于这个魔术，我们还需要 kotlin android 扩展。

![](img/e579f27be7d8d985a8c3ec8610a2a7f9.png)

## 第一步

让我们以通用的方式创建一个抽象适配器

我们需要向构造函数传递实际的项目列表和布局资源 id

```
 **abstract class** AbstractAdapter<ITEM> **constructor**(
        **protected var itemList**: List<ITEM>,
        **private val layoutResId**: Int)
    : RecyclerView.Adapter<AbstractAdapter.Holder>() {

    **class** Holder(itemView: View) : RecyclerView.ViewHolder(itemView)
}
```

此外，我们需要创建从基本视图持有人扩展的视图持有人，并实现我们的适配器的 onBindViewHolder 和 onCreateViewHolder，就像我们在创建 recyclerview 时总是做的那样。

```
**override fun** getItemCount() = **itemList**.**size

override fun** onCreateViewHolder(parent: ViewGroup,
                                viewType: Int): Holder {
    **val** view = parent *inflate* **layoutResId
    return** Holder(view)
}

**override fun** onBindViewHolder(holder: Holder, position: Int) {
    **val** item = **itemList**[position]
    holder.**itemView**.*bind*(item)
}

**protected open fun** View.bind(item: ITEM) {
}

**class** Holder(itemView: View) : RecyclerView.ViewHolder(itemView)
```

而且我们还需要创建从我们的抽象适配器扩展而来的 adapter，不要慌，比较短，姑且命名为 Kadapter 吧。

```
**class** Kadapter<ITEM>(items: List<ITEM>,
                     layoutResId: Int,
                     **private val bindHolder**: View.(ITEM) -> Unit)
    : AbstractAdapter<ITEM>(items, layoutResId) {

    **override fun** onBindViewHolder(holder: Holder, position: Int) {
        holder.**itemView**.**bindHolder**(**itemList**[position])
    }
}
```

## 第二步

让我们看看如何使用我们的高级适配器:

```
**val** adapter = Kadapter(users, R.layout.*item_layout*) **{** nameText.*text* = **it**.**name** surNameText.*text* = **it**.**surname
}** recyclerView.*adapter* = adapter
recyclerView.*layoutManager* = LinearLayoutManager(**this**)
```

我们可以就这么用，或者更深入一点。为此，让我们继续前进，添加更多的东西。

## 超级英雄登陆！他要做超级英雄着陆了！

让我们创建一个 recycler 视图的扩展。

```
**fun** <ITEM> RecyclerView.setUp(items: List<ITEM>,
                              layoutResId: Int,
                              bindHolder: View.(ITEM) -> Unit,
                              manager: RecyclerView.LayoutManager = LinearLayoutManager(**this**.*context*)): Kadapter<ITEM> {

    **return** Kadapter(items, layoutResId, **{** bindHolder(**it**)
    **}**).*apply* **{** *layoutManager* = manager
        *adapter* = **this
    }** }
```

它将封装适配器和布局管理器，我们也可以改变布局管理器的默认实现。

## 第三步

![](img/eefe35e6e1cd8221da294f146e3f762c.png)

到目前为止，一切都很好，但是如果我们尝试单击列表中的项目，什么都不会发生。我们需要注意这一点。让我们实现 onClickListener。

为此，我们需要在抽象适配器中添加点击逻辑。我们的 onCreateViewHolder 变成了这样。

```
**override fun** onCreateViewHolder(parent: ViewGroup, viewType: Int): Holder {
    **val** view = parent *inflate* **layoutResId
    val** viewHolder = Holder(view)
    **val** itemView = viewHolder.**itemView** itemView.setOnClickListener **{
        val** adapterPosition = viewHolder.*adapterPosition* **if** (adapterPosition != RecyclerView.*NO_POSITION*) {
            **onItemClick**(itemView, adapterPosition)
        }
    **}
    return** viewHolder
}**protected open fun** onItemClick(itemView: View, position: Int) {
}
```

我们还需要将 onClick 逻辑添加到我们之前创建的适配器实现和扩展中。kadapter 的最终代码如下所示:

```
**class** Kadapter<ITEM>(items: List<ITEM>,
                     layoutResId: Int,
                     **private val bindHolder**: View.(ITEM) -> Unit)
    : AbstractAdapter<ITEM>(items, layoutResId) {

    **private var itemClick**: ITEM.() -> Unit = **{}

    constructor**(items: List<ITEM>,
                layoutResId: Int,
                bindHolder: View.(ITEM) -> Unit,
                itemClick: ITEM.() -> Unit = **{}**) : **this**(items, layoutResId, bindHolder) {
        **this**.**itemClick** = itemClick
    }

    **override fun** onBindViewHolder(holder: Holder, position: Int) {
        holder.**itemView**.**bindHolder**(**itemList**[position])
    }

    **override fun** onItemClick(itemView: View, position: Int) {
        **itemList**[position].**itemClick**()
    }
}
```

和用于扩展的最终代码

```
**fun** <ITEM> RecyclerView.setUp(items: List<ITEM>,
                              layoutResId: Int,
                              bindHolder: View.(ITEM) -> Unit,
                              itemClick: ITEM.() -> Unit = **{}**,
                              manager: RecyclerView.LayoutManager = LinearLayoutManager(**this**.*context*)): Kadapter<ITEM> {
    **return** Kadapter(items, layoutResId, **{** bindHolder(**it**)
    **}**, **{** itemClick()
    **}**).*apply* **{** *layoutManager* = manager
        *adapter* = **this
    }** }
```

现在让我们看看我们取得了什么成就。

我们可以创建不带点击监听器和带点击监听器的适配器。

```
recyclerView.*setUp*(users, R.layout.*item_layout*, **{** nameText.*text* = **it**.**name** surNameText.*text* = **it**.**surname
}**)
```

点击一下

```
recyclerView.*setUp*(users, R.layout.*item_layout*, **{** nameText.*text* = **it**.**name** surNameText.*text* = **it**.**surname
}**, **{** toast(**"Clicked $name"**)
**}**)
```

作为奖励，我们可以添加基本方法到我们的抽象适配器，如添加，删除，更新，当然使用 diffUtils。

本文的底线是展示 [Kotlin](https://hackernoon.com/tagged/kotlin) + [Android](https://hackernoon.com/tagged/android) 的一些强大特性，这些特性使得代码更加灵活。现在如果有兴趣可以更进一步。

**感谢您阅读本文。你可以在** **那里找到完整的代码** [**。**](https://github.com/armcha/Kadapter)

**让我们在**[**Twitter**](https://twitter.com/ArmanChatikyan)**[**Github**](https://github.com/armcha/)**[**脸书**](https://web.facebook.com/chatikyana) **上成为朋友吧。如果你喜欢这些文章，那么请用下面的掌声推荐这篇文章，让其他人也能看到。******

****我们也很乐意听到你的意见和建议:)****

****谢谢。****