# Lightning 组件的拖放

> 原文：<https://medium.com/hackernoon/drag-drop-for-lightning-components-27230745a2eb>

# 很久以前…

一位同事和我谈论基于组件的应用程序；我和他分享了一些我和 AngularJS(是的，“1”版本)和 Vue 的可爱经历。那是 11 月，天气越来越暖和，夏天就在眼前，这本身就让我很开心。然而，我的朋友感到寒冷和沮丧；在过去的几个月里，他一直在从事基于 Lightning 组件框架的 sales force Lightning 应用程序的工作，并且他一直在努力处理那些在任何其他框架中都很自然的东西。他在为 Lightning 组件进行拖放工作时遇到了问题，并且陷入了死胡同……那东西拒绝工作，他要怪 Lightning。

那时，在听到他的誓言后，我只是走开，继续我的路，只给他留下一句简单的“对不起”(又名。“我认为这是你的问题，我绝不可能插手”)。

但和往常一样，这个世界转啊转，有时会让你正好面对你曾经选择忽视和逃避的问题。对于一般的生活来说是如此，对于编程来说也是如此。几个月后，我被同样的问题困扰:**拖&下降为闪电组件**。

# 设定我们的目标！

我认为设立一个小项目作为概念验证是有意义的，也是隔离 D&D 问题的一种手段。

这个想法是将 Trello 和其他看板风格的应用程序的典型的基于列的布局拿出来，创建一些“卡片”或项目，并能够在列之间移动它们。

这是我们需要的:

1.  一个 lightning 应用程序和一个用于我们演示的容器 lightning 组件。
2.  每个列或管道都将被表示为一个组件，并且都将是我们可拖动项目的有效拖放目标
3.  每列中可拖动的项目看起来像卡片，我们可以用鼠标随意移动它们。

很简单，不是吗？

# 准备好了…

首先，我定义了一个非常简单的应用程序和主要组件。其结构如下:

```
<!-- DragDropContainer.cmp -->
<aura:component>
  <!-- resources -->
  <ltng:require styles="{!$Resource.bulma + '/bulma-0.4.2/css/bulma.css'}" /><!-- attributes -->
  <aura:attribute name="allItems" type="list"></aura:attribute><!-- event handlers -->
  <aura:handler name="init" action="{!c.doInit}" value="{!this}"></aura:handler>
  <aura:handler name="pipelineChange" event="c:pipelineChange" action="{!c.onPipelineChanged}"></aura:handler><div class="columns">
    <c:pipeline title="New" items="{!v.allItems}"></c:pipeline>
    <c:pipeline title="Closed" items="{!v.allItems}"></c:pipeline>
    <c:pipeline title="Deleted" items="{!v.allItems}"></c:pipeline>
  </div>
</aura:component>
```

尽可能简单地，我们有 3 个“管道”来保存我们的可拖动项，它们是在组件的“onInit”方法中生成的，还有一个名为“pipelineChange”的自定义事件的处理程序。该事件有以下定义:

```
<!-- pipelineChange.evt -->
<aura:event type="COMPONENT" description="Event fired whenever an item is dropped on a pipeline">
    <aura:attribute type="String" name="title"></aura:attribute> 
    <aura:attribute type="Object" name="item"></aura:attribute></aura:event>
```

**这是我们的控制器，展示了我们如何创建项目和处理自定义事件**

```
<!-- DragDropContainerController.js -->
({
  doInit: function(component, event, helper) {
    var newItems = [
      {
        title: "One item",
        id: "23243342",
        status: "New"
      }
    ];
    component.set("v.allItems", newItems);
  },onPipelineChanged: function(component, event, helper) {
    var title = event.getParam("title");
    var item = event.getParam("item");
    var allLists = component.get("v.allItems");
    var actualItem = allLists.find(function(el) {
      return el.id == item.id;
    });
    if (actualItem) {
      actualItem.status = title;
      component.set("v.allItems", allLists);
    } else {
      console.log("could not find item ", item, " in list ", allLists);
    }
  }
});
```

因此，基本上我们创建了一个条目列表，每个条目都有一个与组件中的每个管道(列)相匹配的“状态”。为了处理 **pipelineChanged** 事件，我们定义了一个处理程序，它将项目的状态设置为它所放入的列的名称，并刷新我们的集合。我知道这不是最理想的，但现在已经足够了。

# 设置…

每个项目将由一个“卡”组件表示，如下所示:

```
<!-- Card.cmp -->
<aura:component >
    <aura:attribute name="item" type="Object" required="true"></aura:attribute>
    <div class="card draggable" draggable="true" ondragstart="{!c.onDragStart}">
        <div class="card-content">{!v.item.title}</div>
    </div>
</aura:component>
```

我们将组件的主标签声明为“draggable ”,以便浏览器知道我们希望用户能够拖动它，并且我们有一个用于 **dragStart** 事件的处理程序。正如您可能已经猜到的，我们将把我们的项目(组件的属性)放入 D & D 的 dataTransfer 事件集属性:

```
<!-- CardController.js -->
({
     onDragStart : function(component, event, helper) {
         event.dataTransfer.dropEffect = "move";
         var item = component.get('v.item');
         event.dataTransfer.setData('text', JSON.stringify(item));
     }
})
```

太好了！我们能够将序列化为 JSON 字符串的整个项目放入 D&D 事件集的 dataTransfer 属性中(有关这方面的更多信息，请参见 [HTML 拖放 API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API) )。现在，我们只需要确保我们的“管道”组件能够在用户放下对象时进行处理，我们就完成了。

# 还有…什么？

通常，为了让 drop 事件在给定的 HTML 元素上工作，我们只需要为接收事件并调用“event.preventDefault()”的 **ondrop** 事件实现一个简单的处理函数。因此，我为我们的管道创建了以下组件

```
<!-- pipeline.cmp -->
<aura:component >
    <!-- Attributes -->
    <aura:attribute name="title" type="String" required="true"></aura:attribute>
    <aura:attribute name="items" type="Object[]" required="true"></aura:attribute>

    <!-- Custom Events -->
    <aura:registerEvent name="pipelineChange" type="c:pipelineChange"/>

    <div class="column" ondrop="{!c.onDrop}">
        <div class="panel">
            <div class="panel-heading">
                {! v.title }
            </div>
            <div class="panel-block column">
                <aura:iteration items="{!v.items}" var="item">
                    <aura:if isTrue="{!item.status == v.title}">
                     <c:Card item="{!item}"></c:Card>
                    </aura:if>    
                </aura:iteration>
            </div>
        </div>
    </div>
</aura:component>
```

注意，我注册了前面提到的“pipelineChange”事件，所以我可以从这个组件中触发它(毕竟，我们希望我们的主组件在用户将卡放入其中一个管道时能够注意到。这是我们的控制器:

```
<!-- pipelineController.js -->
({  
    onDrop: function(component, event, helper) {
        event.preventDefault();
        var pipelineChangeEvent = component.getEvent('pipelineChange');
        pipelineChangeEvent.setParams({
            'title': component.get('v.title'),
            'item': JSON.parse(event.dataTransfer.getData('text'))
        });
        pipelineChangeEvent.fire();
    }
})
```

迄今为止，太阳底下没有什么新鲜事。我用一个最终触发 **pipelineChange** 事件的函数处理了 **ondrop** 事件，这样我们的主组件就可以施展魔法并刷新我们的列表(同样，所有这些往返都是因为我想更加简单，而不是使实现过于复杂。应该管用吧？

***嗯，情况就是这样行不通。***

# 又是糟糕的设计，闪电？

这并不新鲜，Lightning 组件框架的某些部分并没有得到很好的考虑，而且它们的执行更差。兔子洞有多远是我们在开发 lightning 应用程序和组件时每天都会发现的事情，所以 Salesforce 搞乱本机 [javascript](https://hackernoon.com/tagged/javascript) 事件令人惊讶。

**众所周知** lightning 覆盖了“触摸”事件，在基于触摸的平台上用“点击”来代替它们，这并没有那么糟糕，如果你仔细想想，这甚至是有道理的。然而，D 事件的处理却是另一回事。

显然，有些事件就是不行，比如“**拖拉**”和“**拖拉**”。这是为什么，我不知道。更糟糕的是，一些事件被渲染成无法尊重他们各自的契约。O 一个这样的例子是“ **drop** ”，如果没有以非常具体的方式处理，它将丢失其 key **dataTransfer** 属性的值。

如果你没有注意到**数据传输**属性是整个拖放机制工作的关键。如果我们不能获得我们在链的前面步骤中存储的任何数据，我们就不可能成功地处理一个 drop 事件…这意味着我们破产了。

# 追逐龙

然后，我在谷歌上搜索了一大堆误导性的东西，有限的用例，甚至是我拒绝为处理拖放用例这样简单和基本的事情添加的库覆盖的东西。

可能就像流行故事中的 Jekyll 博士一样，我开始混合药水和我知道会有效的拖放实现的配方，甚至在 VueJS 上创建了这个概念的[证明。](https://codepen.io/srodriki/pen/KqKEmO)什么都没用，我正要放弃的时候…

我决定**阻止所有我没有使用的拖拽事件的默认设置**,因为你猜怎么着？这东西起作用了！

我将为您省去一些往返行程，并告诉您我实际上需要用 **preventDefault** 处理的另一个事件是 **dragover** 事件。这是为什么呢？我仍然不知道。

最后，我们的管道组件将如下所示:

```
<!-- pipeline.cmp -->
<aura:component >
    <!-- Attributes -->
    <aura:attribute name="title" type="String" required="true"></aura:attribute>
    <aura:attribute name="items" type="Object[]" required="true"></aura:attribute>

    <!-- Custom Events -->
    <aura:registerEvent name="pipelineChange" type="c:pipelineChange"/>

    <div class="column" ondrop="{!c.onDrop}" ondragover="{!c.allowDrop}">
        <div class="panel">
            <div class="panel-heading">
                {! v.title }
            </div>
            <div class="panel-block column">
                <aura:iteration items="{!v.items}" var="item">
                    <aura:if isTrue="{!item.status == v.title}">
                     <c:Card item="{!item}"></c:Card>
                    </aura:if>    
                </aura:iteration>
            </div>
        </div>
    </div>
</aura:component>
```

这是我们的控制器，现在可以让我们的组件按预期工作。

```
<!-- pipelineController.js -->
({
 allowDrop: function(component, event, helper) {
        event.preventDefault();
    },

    onDrop: function(component, event, helper) {
        event.preventDefault();
        var pipelineChangeEvent = component.getEvent('pipelineChange');
        pipelineChangeEvent.setParams({
            'title': component.get('v.title'),
            'item': JSON.parse(event.dataTransfer.getData('text'))
        });
        pipelineChangeEvent.fire();

    },
})
```

# 旅程还在继续

我希望这篇相当长的文章能帮助一些人理解如何成功地处理 Lightning 组件的拖放功能，但我也希望它能描述这样一种挣扎，即 Lightning 组件框架常常使事情复杂化，而不是作为一种在 Force.com 平台上构建丰富的 web 应用程序的手段。

> 你可以在 [Github](https://github.com/srodriki/sfdc-lightning-drag-drop/) 中找到这个项目的完整回购

许多其他例子向我们展示了 Lightning 中糟糕的设计决策，大多数时候这让我想到，如果有一个框架能够简单地提供非常有用的库*和调用由 Apex 控制器从任何 Javascript*公开的 ***AuraEnabled 方法，将任何前端工作留给 Vue、Angular、React、Inferno、Ember 等经验证的库，我们会更高兴。*****

**不管怎样，虽然上述情况不会发生，但我会(希望)尽快为 Lightning 组件框架的常见问题提供有用的解决方案。**

****干杯，快乐编码！****

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**