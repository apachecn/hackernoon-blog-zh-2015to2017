# 阿波罗的 GraphQL 嵌套突变:小疲劳，最大增益

> 原文：<https://medium.com/hackernoon/graphql-nested-mutations-with-apollo-small-fatigue-max-gain-1020f627ea2e>

# 现场

我正在做一些实验，我想在 React 项目中用 GraphQL 做些尝试。我已经对 GraphQL 有所了解，但我从未真正自己使用过它。

我开始寻找一个好的 GraphQL 后端，我选择了目前最好的 graph cool(T2 ):有很多指南和教程，可以与其他服务集成，非常灵活，对开发者免费。亮绿灯！

Graphcool 有针对 [Apollo](http://www.apollodata.com/) 和 Relay 的快速入门指南，但我已经决定使用什么，所以我开始在我的 React 项目中使用它:

```
"apollo-client": "^0.8.1",
"graphql-tag": "^1.2.3",
"react-apollo": "^0.9.0",
```

# 任务

我想一些不那么原创但总是很有趣的东西:乐队、专辑、歌曲等等..总是比 [Northwind Traders 或 Contoso Ltd.](https://en.wikipedia.org/wiki/List_of_fictional_Microsoft_companies) :)更令人兴奋

所以我只想从/向 Graphcool 读取/写入数据。

在 GraphQL 中，当你想读的时候你需要[查询](http://dev.apollodata.com/react/queries.html)，当你想写的时候你需要[突变](http://dev.apollodata.com/react/mutations.html)。

*我写的不是查询，因为这个故事的重点是嵌套突变，因为查询得到了很好的解释，我在实现过程中没有遇到任何问题。*

让派对开始吧

用 Graphcool“控制台”(一个很棒的数据库建模界面)，我创建了一个粗略的结构。

```
type Band {
  name: String!
  country: String
  website: String
  members: [Person]
  contact: Person
  albums: [Album]
}type Person {
  firstName: String!
  lastName: String!
}type Album {
  name: String!
  year: Int!
  songs: [Song]
}type Song {
  trkNr: Int
  title: String!
  duration: String
}
```

如您所见，我们从乐队模型的相册字段开始，在两个不同的级别上嵌套了两个数组。

所以，为了清楚起见，我想使用一些关于 STORMO 音乐乐队的数据来存储所有这些信息，并且我想用一个单独的突变来实现它！

# 问题是

派对突然崩溃(STORMO 停止播放！)当我意识到我不知道如何创建嵌套变异，也没有(无论如何非常好的)Apollo 文档可以帮助我时。

此外，我有足够的勇气使用数组，现在我的眼睛迷失在窗口中… `the physical one!!`

# 帮助

在一些不值得一提的尝试之后，我决定使用[salvitic Graphcool slack support](https://slack.graph.cool/)，在那里我开始在通用频道跟踪。

Graphcool 员工 Nilan Marktanner 立即开始帮助我，当我最终以一种可理解的方式解释了我的情况后，他递给我…

# 解决方案

感谢尼兰，我明白了三件事:

*   及时援助的重要性，是的，还有它的美妙之处
*   [graph cool 上的嵌套突变得到改进，更易于使用](https://www.graph.cool/blog/improving-dx-with-nested-mutations-vietahx7ih)
*   当你写一个嵌套的变异时，没有必要写嵌套对象的类型！

这正是我所需要的。

# 代码时间

我必须制造的变异

```
mutation {
  createBand(
    name: "Stormo"
    country: "Italy"
    website: "[https://stormo.bandcamp.com/](https://stormo.bandcamp.com/)"
    members: [{
      firstName: "Luca"
      lastName: "Rocco"
    }, {
      firstName: "Federico"
      lastName: "Trimeri"
    }, {
      firstName: "Giacomo"
      lastName: "Rento"
    }, {
      firstName: "Gabriele"
      lastName: "Coldepin"
    }]
    contact: {
      firstName: "Giacomo"
      lastName: "Rento"
    }
    albums: [{
      name: "SOSPESI NEL VUOTO BRUCEREMO IN UN ATTIMO E IL CERCHIO SARÀ CHIUSO"
      year: 2014
      songs: [{
        trkNr: 1
        title: "In Volo"
        duration: "02:50" 
      }, {
        trkNr: 2
        title: "Supernova"
        duration: "02:10" 
      }, {
        trkNr: 3
        title: "Fuga"
        duration: "01:24" 
      }, {
        trkNr: 4
        title: "Perchè La Bambina Cade"
        duration: "02:46" 
      }]
    }, {
      name: "7"
      year: 2009
      songs: [{
        trkNr: 1
        title: "Incosiderata Putrefazione"
        duration: "03:18"
      }, {
        trkNr: 2
        title: "Abbandono La Mia Volontà"
        duration: "03:20"
      }]
    }, {
      name: "Self-Titled EP"
      year: 2007
      songs: [{
        trkNr: 4
        title: "Quando Non Ci Sei"
        duration: "02:08" 
      }, {
        trkNr: 5
        title: "Al Punto Di Non Ritorno"
        duration: "04:36" 
      }]
    }]
  ) {
    id
    members {
      id
    }
    contact {
      id
    }
    albums {
      id
      songs {
        id
      }
    }
  }
}
```

使用 Apollo Client，我用 GraphQL 变量指定输入参数，所以让我们看看它在这种情况下是怎样的:

```
import gql from 'graphql-tag';const BandMutation = gql`
  mutation (
    $name: String!
    $country: String
    $website: String
    $Members: [Person]
    $Contact: Person
    $Albums: [Albums]
  ) {
    createBand(
      name: $name
      Country: $Country
      Members: $Members
      Contact: $Contact
      Albums: $Albums
    ) {
      id
      members {
        id
      }
      contact {
        id
      }
      albums {
        id
        songs {
          id
        }
      }
    }
  }
`;
```

使用一个表单，我像这样组合数据:(但是注意，apollo 想要这个名为'*变量*的对象，而不是别的！)

```
const variables = {
  name: "Stormo",
  country: "Italy",
  website: "[https://stormo.bandcamp.com/](https://stormo.bandcamp.com/)"
  members: [{
    firstName: "Luca",
    lastName: "Rocco",
  }, {
    firstName: "Federico",
    lastName: "Trimeri",
  }, {
    firstName: "Giacomo",
    lastName: "Rento",
  }, {
    firstName: "Gabriele",
    lastName: "Coldepin",
  }],
  contact: {
    firstName: "Giacomo",
    lastName: "Rento",
  },
  albums: [{
    name: "SOSPESI NEL VUOTO BRUCEREMO IN UN ATTIMO E IL CERCHIO SARÀ CHIUSO",
    year: 2014,
    songs: [{
      trkNr: 1,
      title: "In Volo",
      duration: "02:50" ,
    }, {
      trkNr: 2,
      title: "Supernova",
      duration: "02:10" ,
    }, {
      trkNr: 3,
      title: "Fuga",
      duration: "01:24" ,
    }, {
      trkNr: 4,
      title: "Perchè La Bambina Cade",
      duration: "02:46",
    }]
  }, {
    name: "7",
    year: 2009,
    songs: [{
      trkNr: 1,
      title: "Incosiderata Putrefazione",
      duration: "03:18",
    }, {
      trkNr: 2,
      title: "Abbandono La Mia Volontà",
      duration: "03:20",
    }]
  }, {
    name: "Self-Titled EP",
    year: 2007,
    songs: [{
      trkNr: 4,
      title: "Quando Non Ci Sei",
      duration: "02:08",
    }, {
      trkNr: 5,
      title: "Al Punto Di Non Ritorno",
      duration: "04:36",
    }]
  }]
}
```

最后，在我的 React 组件中，我可以使用所有这些将我的数据发送到 Graphcool

```
import React, { Component } from 'react';
import { graphql, compose } from 'react-apollo';
import BandMutation from './../graphql/BandMutation';class CompanySettings extends Component {
  constructor(props) {
    super(props);
    this.sendData = this.sendData.bind(this);
  }sendData(variables) {
    this.props.CreateBand({ variables })
    .then((response) => {
      window.alert('You submitted data about a new band');
    }).catch((e) => {
      console.error(e);
    });
  }render() {
    return <FormCreateBand onSubmit={this.sendData} />;
  }
}export default compose(
  // with this I can use the mutation with this.props.CreateBand
  graphql(BandMutation, { name: 'CreateBand' }),
  // I can write here other mutations or queries
)(CompanySettings);
```

# 结论…但是等等！这实际上是开始

*   GraphQL 不是未来，而是现在，它是如此的好，在爱的记忆中休息
*   Graphcool 是一个很棒的服务，它的员工非常细心和活跃(我很确定他们也不睡觉)
*   是不是有些疲劳？…是的，当然！但是有了这种疲劳，你投入的每一分钟时间，以后都会省下很多，这取决于项目。

P.S. **晚会现在要疯狂了，感谢** [**风暴{O}**](https://stormo.bandcamp.com/) **！！**

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！