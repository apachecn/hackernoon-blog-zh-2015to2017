# 带有 Rails 的 GraphQL Api

> 原文：<https://medium.com/hackernoon/graphql-api-with-rails-faab252aaffa>

Graphql 是一项令人敬畏的新技术，它让客户端开发者更容易快速迭代和批量处理网络请求。

我真的很喜欢 GraphQl 提供的承诺，但是它不像 rest 那么容易
立即开始运行。这篇博文
展示了如何使用 rails 通过几个步骤构建一个工作的 GraphQL 后端。

我正在使用我自己的项目 [graphql-api](https://github.com/coldog/graphql-api) 作为构建 graphql 后端的框架。我们将看看如何构建一个基本的博客应用程序。一个博客会有一个作者和一些标签。

首先创建 rails 后端。

```
rails new blog-api --api
```

将必要的库添加到 gem 文件中

```
gem 'graphql'
gem 'graphql-api'
```

创建我们的模型

```
rails g model Author name:string
rails g model Blog title:string content:text author:references
rails db:migrate
```

添加我们的与 author 类有许多关系

```
class Author
  has_many :blogs
end
```

太好了！现在我们有了博客后端的基本设置。让我们添加一些
控制器来服务基本的 Graphql 查询。

将以下文件添加到***app/controllers/graph QL _ controller . Rb***中。

```
class GraphqlController < ApplicationController
  SCHEMA = GraphQL::Api::Schema.new.schema def create
    render json: SCHEMA.execute(
      params[:query], 
      variables: params[:variables] || {},
    )
  end

end
```

将以下路线添加到***config/routes . Rb***

```
resources :graphql, only: :create
```

现在让我们用 curl 测试新的 api。启动服务器并运行下面的
命令，看看我们是否有博客文章

```
curl -XPOST -d 'query=query { blogs { id } }' \
     localhost:3000/graphql{"data":{"blogs":[]}}
```

让我们创建一个作者

```
curl -XPOST \
  -d 'query=mutation { 
         createAuthor(input: {name: "foobar"}) { 
            author { id } 
     }}' localhost:3000/graphql{"data":{"createAuthor":{"author":{"id":1}}}}
```

现在让我们用作者的 id 创建一个博客

```
curl -XPOST \
  -d 'query=mutation { 
         createBlog(input: {title: "foobar", author_id: 1}) { 
             blog { id } 
      }}' localhost:3000/graphql{"data":{"createBlog":{"blog":{"id":1}}}}
```

让我们再次尝试第一个查询，但是这次让我们尝试返回博客作者以及博客名称。

```
curl -XPOST -d 'query=query { 
        blogs { 
          id
          title
          content
          author {
            name
          }
        }
     }' localhost:3000/graphql
```

你也可以运行 ***更新博客*** 突变以及 ***更新作者*** 突变。试着删除一个作者或博客，看看你会得到什么回应。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[赞/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！