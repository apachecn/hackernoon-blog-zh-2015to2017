# 使用 ngrx/Redux 和 Angular 2 我学到了什么

> 原文：<https://medium.com/hackernoon/what-i-have-learned-using-ngrx-redux-with-angular-2-20a748149661>

![](img/b8405415eab50794ac894b8e481d1161.png)

包括我在内的大部分 Angular 开发者都有一些后端编程背景。它有一些优点，但它也对我们如何解决某些问题有一些影响。我们把世界看成是一堆物体，它们是责任的容器。在 redux 中，你必须稍微转换一下你的思维模式。

## 不要和样板文件较劲。

当我开始使用 redux 时，我讨厌这些动作创建者和这个又大又丑的 switch 语句。所以我从定义一些受 alt.js 实现启发的约定开始。然后，我实现了如下内容

```
**export class** SavedConfigurationStore {

  *setBusy*(state, _, busy) {
    **return ** {...state, busy);
  }

  *invalidate*(state) {
    **return {**...state, invalidate**: true}**;
  }

  *onFetchSavedConfigsSuccess*(state, action) {
    **return {...**state, configs**:** action.payload, invalidate**: false**};
  } ....}**const** savedStore **= new** SavedConfigurationStore();
**export const** savedConfigurationReducer **=** ReducerBinder.*start*()
  .*bindActionsByConvention*(SavedActions, savedStore)
  .*bindAsyncAction*(SavedActions.*FETCH_SAVED_CONFIGS*, savedStore.*setBusy*)
```

我创建它只是为了让简单的 POJO 对象具有动作。然后我定义了这个 ReducerBinder 类来绑定我的对象方法作为动作处理程序。尽管它看起来对后端开发人员更有吸引力，但它也有很多缺点。除了我没有人知道这个惯例，如果我强迫我的团队成员基于这个 redux 包装器来构建整个应用程序，他们很快就会恨我。其次，它使用面向对象模式。Redux 是关于使用功能方法，将所有的东西分成小的可组合的功能。在上面的例子中，我们把所有的东西都放在一个大的对象中。

redux 附带的这个样板文件利大于弊。你有完全的控制权。您将会真正感到惊讶，使用该架构实现离线状态、重做/撤消功能、乐观更新功能以及更多功能是多么容易。没有理由为此而战。如果你想完全控制你的代码，你就不要用抽象来覆盖一切。

**让你的状态正常化**

你应该保持你的减速器相对较小和紧凑。当你的状态没有正常化，你把所有东西都放在一个大模型里，结果会是一团糟。在你的 reducer 中，你会有 lodash 提供的一大堆工具来遍历这些大对象树，找到你想要更新的对象。当你最终找到叶对象，你想要更新你需要为每个父对象创建新的对象直到你的模型的根。

```
**const** initialState **=** {
  genres**:** [
    {
      id**:** 1, name**:** *'rock'*, bands**:** [
      {
        id**:** 1, name**:** *'led zeppelin'*, albums**:** [
        {id**:** 1, name**:** *'I'*, items**:** 3},
        {id**:** 2, name**:** *'II'*, items**:** 66},
        {id**:** 3, name**:** *'III'*, items**:** 3},
      ]
      },
      {id**:** 2, name**:** *'boston'*},
    ]
    }
  ]
};

**export const** *albums* **=** (state = initialState, {*type*, payload}) **=>** {
  **switch** (*type*) {
    **case** UPDATE_ITEMS_COUNT**:
      const** nextGenres **=** state.genres.*map*(g **=>** {
        **const** nextBands **=** g.bands.*map*(b **=>** {
          **return** b.albums.*map*(a **=>** {
            **if** (a.id **!=** payload.id) {
              **return** a;
            }
            **return** {...a, items**:** payload.items};
          });
        });
        **return** {...g, bands**:** nextBands}
      });

      **return** {...state, genres**:** nextGenres};
    **default:
      return** state
  }
};
```

这就是你的代码看起来的样子。当你将你的状态正常化时，你将有三个缩减者:流派、乐队、专辑。你的责任会被合理分配。Albums reducer 将负责对相册模型进行更新。流派和乐队减少器可以负责将专辑从一个组转移到另一个组。

```
**const** initialState **=** {
  [1]**:** {id**:** 1, name**:** *'I'*, items**:** 3},
  [2]**:** {id**:** 2, name**:** *'II'*, items**:** 66},
  [3]**:** {id**:** 3, name**:** *'III'*, items**:** 3}
};

**export const** *albums* **=** (state **=** initialState, {*type*, payload}) **=>** {
  **switch** (*type*) {
    **case** UPDATE_ITEMS_COUNT**:
      const** {id, items} **=** payload;
      **const** album **=** state[id];
      **return** {...state, [id]**:** {...album, items}};
    **default:
      return** state
  }
};
```

上面你可以看到归一化的 reducer 是什么样子。值得注意的是，您不仅有扁平的结构，而且您只将所有对象保存在“字典”中，而不是将所有内容保存在数组中。当您拥有以关键字作为 id 的字典时，找到要更新的对象就容易多了。

**我怎样才能使状态正常化？**

这可以在服务类实现中手动完成。或者你可以使用第三方库来帮助你。我强烈推荐去看看 [normalizr](https://github.com/paularmstrong/normalizr) 库。使用 normlizr，您可以定义从下面的服务器链接中检索的模式数据:

```
import { normalize, schema } from 'normalizr';

const album = new schema.Entity('album');
const bands = new schema.Entity('bands', {
  albums: [album]
});
const genres = new schema.Entity('genres', { 
  bands: [ bands]
});

const normalizedData = normalize(originalData, genres);
```

结果将如下所示

```
**var** a **=** {
  result**:** *"123"*,
  entities**:** {
    *"genres"***:** {
      *"123"***:** {id**:** 1, name**:** *'rock'*, bands**:** [1, 2]}
    },
    *"albums"***:** {
      *"1"***:** {id**:** 1, name**:** *'I'*, items**:** 3},
      *"2"***:** {id**:** 2, name**:** *'I'*, items**:** 3},
      *"3"***:** {id**:** 3, name**:** *'I'*, items**:** 3}
    },
    *"bnads"***:** {
      *"1"***:** {id**:** *"1"*, name**:** *'led zeppelin'*, albums**:**[*"1"*, *"2"*, *"3"*]},
      *"2"***:** {id**:** *"1"*, name**:** *'boston'*, albums**:** []},
    }
  }
};
```

**如何对视图的状态进行反规范化？**

然而，规范化简化了更新操作，却使读取变得更加复杂。但是它也给了你更多的灵活性。当您的一个视图需要连接两个实体，而另一个视图需要连接另外三个实体时，那么您可以相对容易地做到这一点。这对你的表现也有很好的影响。当您的视图只需要 2 个实体作为视图模型，并且您正在更新第三个实体时，则您的视图不会被不必要地刷新。但是您必须使用 onPush 通知模式，并且必须灵活地对视图模型的数据进行反规范化。

我正在寻找一些建议，没有找到任何有帮助的东西，所以我做了我的反规格化状态。如果有人有更好的方法，请让我知道。

假设我们希望视图以与初始模型完全相同的形状呈现我们的数据:

```
<div class="genre"  *ngFor="let genre of genres">
  <div class="band" *ngFor="let band of genre.bands">
    <div class="album" *ngFor="let album of band.albums">{{genre.name}}{{band.name}}:{{album.name}}</div>
    <img  [attr.src]="album.coverImage" />
  </div>
</div>
```

现在我们不能这样做，因为我们的模型是标准化的。例如，流派模型只有乐队 id 列表。我们可以通过定义如下的映射选择器来实现:

```
**const** *getGenres* **=** state**=>**{
  **const** {albums, genres, bands} **=** state.entities;
  **return** values(genres).map(g**=>**{
    **const** bands **=** g.bands.map(bid**=>** {
      **const** band **=** bands[bid];
      **const** bandAlbums **=** band.albums.map(aid**=>**{
        **const** a **=** albums[aid];
        **return** {...a, coverImage **:** *`images/*${a.name}*.jpg`*}
      });
      **return** {...band, albums**:** bandAlbums};
    })
  })
};

store.select(*getGenres*).*subscribe*((g)**=>**{
  **this**.genres **=** g;
})
```

但是我不建议你这么做，有两个原因:

*   我们在这里混合了两个关注点——遍历对象树和映射到视图模型。
*   每当一个单独的相册被更新，angular 将重新创建整个视图模型树作为新的实例，这将导致整个事情的重新渲染，即使我们使用 onPush 策略。

为了打破绩效和划分责任，我们可以做如下事情:

```
@Component({
  template**:** *`   
    <div class="genre"  *ngFor="let genre of (genres$|async)">
        <genre-view [genre]="gendre"></genre-view>
    </div>`* })
**export class** HomeComponent {
  genres$;

  **constructor**(store**:** Store**<**AppState**>**) {
    **this**.genres$ **= this**.store.*select*(getAllGenres);
  }
}
```

然后在 GenreComponent 中，我们有如下代码

```
@Component({
  selector**:** *'genre-view'*,
  template**:** *`
<div class="band" *ngFor="let band of (bands$|async$)">
  <band-view [band]="band" [genre]="genre"></band-view>
</div>
`* })
**export class** GenreComponent {
  @Input() genre;
  bands$;
  **constructor**(store**:** Store**<**AppState**>**) {
    **this**.bands$ **= this**.store.*select*(getBandsByIds(**this**.genre.bands))
  }
}
```

然后在乐队内部

```
@Component({
  selector**:** *'band-view'*,
  template**:** *`
<div class="album" *ngFor="let album of (albums$|async)">{{genre.name}}{{band.name}}:{{album.name}}
<img  [attr.src]="album.coverImage" />
</div>
`* })
**export class** BandComponent {
  @Input() genre;
  @Input() band;
  albums$;
  **constructor**(store**:** Store**<**AppState**>**) {
    **this**.albums$ **= this**.store.*select*(getAlbumsByIds(**this**.band.ablums)).*map*(a**=>**({
      name**:** a.name,
      coverImage**:** *`*${a.name}*.jpg`* }))
  }
}
```

到目前为止一切顺利。但是这个解决方案有两个问题。我们没有完全从 OnPush 变化检测策略中获益。第二件事是，我们在构造函数中只定义了一次子列表，这意味着当输入参数改变时，我们根本没有反应。当 BandComponent 乐队输入改变时，它仍将呈现属于上一个乐队的专辑。为了解决这个问题，我的方法如下。

```
@Component({
  selector**:** *'band-view'*,
  changeDetection**:** ChangeDetectionStrategy.*OnPush*,
  template**:** *`
<div class="album" *ngFor="let album of props.albums">{{album.name}}
<img  [attr.src]="album.coverImage" />
</div>
`* })
**export class** BandComponent {
  @Input() band;
  band$ **= new** Subject**<**any**>**();
  props **=**{};
  **constructor**(store**:** Store**<**AppState**>**, **private** cd **:** ChangeDetectorRef) {
    **this**.store.let(*queryBandAlbums*(**this**.band$)).map(a**=>**({
      name**:** a.name,
      coverImage**:** *`*${a.name}*.jpg`* })).*subscribe*(a**=>**{
      **this**.props **=** {albums**:** a};
      cd.*markForCheck*();
    });

  }
  *onChanges*(){
    **this**.band$.*next*(**this**.band);
  }
}

**export const** *queryBandAlbums* **=** band$ **=>** store$ **=>** {
  **return** band$.switchMap(b **=>** store$.*select*(getAlbumsByIds(b.ablums)))
    .distinctUntilChanged();
};
```

*   我们使用 Angular onChanges 生命周期钩子来监视输入参数的每一个变化。每次乐队参数改变，我们都会得到新的专辑列表
*   我们使用 OnPush 变化检测策略来获得更好的性能。然而，使用这种策略，angular 将不知道相册对象是否会改变自身。因此，我们需要用 cd.markForCheck()通知 angular 关于更改的信息。

有了这种方法，我们就拥有了两个世界的精华。我们有很好的标准化数据，很容易处理。我们有很好的表现，因为当一些东西将会改变，在只有一个专辑，然后只有这个元素将被重新呈现。所有其他参考将保持不变。让我知道你是否有更好的方法，来处理带角度的归一化状态。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)