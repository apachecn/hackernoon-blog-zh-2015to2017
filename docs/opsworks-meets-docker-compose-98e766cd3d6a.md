# OpsWorks 遇上 docker-compose

> 原文：<https://medium.com/hackernoon/opsworks-meets-docker-compose-98e766cd3d6a>

当我在寻找编排我刚刚创建的应用程序容器的方法时，我发现了 [AWS](https://hackernoon.com/tagged/aws) OpsWorks。那时我正在使用 shell 脚本和木偶。

在开始使用 OpsWorks 后，我意识到它应该是我的集装箱化关键工具，因为它的特性，比如自动修复 EC2 实例、自动缩放和基于时间的 EC2 实例。当然，还有其他解决方案，我仍在尝试，我不得不说，我有点倾向于 kubernetes 或 deis。

无论如何，我认为我可以使用定制的 json OpsWorks 特性来翻译 docker-compose.yml，我们的开发人员已经用它来告诉 [docker](https://hackernoon.com/tagged/docker) 主机应该运行哪些容器映像以及如何运行。

OpsWorks 是一个工具，它使配置和管理主机(EC2 实例)以及将它们与 EC2 ELBs 集成变得容易。基本上，您创建具有应用层(实例、负载平衡器、rds……)的应用程序堆栈，并使用定制的 chef cookbooks 配置它们。您还可以在栈创建时提供一个“自定义 JSON ”,它将被传递给 chef。

定制 JSON 是这里的关键特性，因为它可以用来配置、标记和设置将运行 chef recipes 的 EC2 实例的属性。

我做的一些事情(有 [@tnache](https://bitbucket.org/tnache/opsworks-recipes) 的贡献)是:

*   在启动时更改 EC2 实例标记
*   基于 EC2 实例的当前公共 ip 更新条目中的特定路由 53
*   NFS 山出口

但是这篇文章的主要目标是告诉你我是如何用 docker-compose 完成 docker
容器编排的。

Docker-compose 使用 yaml 文件来声明容器应该如何运行。而且
yaml 文件可以很容易的翻译成 json。

所以，事情是这样的:

```
db:
 image: postgres
web:
 build: my-djangoapp
 command: python manage.py runserver 0.0.0.0:8000
 volumes:
   - /data:/data
 ports:
   - "8000:8000"
 links:
   - db
```

当用 json 表示时，应该是这样的:

```
{
 "db": {
   "image": "postgres"
 },
 "web": {
   "image": "my-djangoapp",
   "command": "python manage.py runserver 0.0.0.0:8000",
   "volumes": [
     "/data:/data"
   ],
   "ports": [
     "8000:8000"
   ],
   "links": [
     "db"
   ]
 }
}
```

简单…

因此，这里的想法是将 YAML 翻译成 json，这样我们就可以在 OpsWorks 堆栈的创建中的自定义 JSON 字段中使用
它，
允许将 JSON 传递给 chef。

当然，我说过 docker-compose 使用 yaml 文件，所以我们必须使用 chef 将它从 json 转换回 yaml，使用 chef 模板非常容易。

因为我想将定制的 json 用于其他事情，而不是简单地
编写 docker-compose.yml，所以我决定将翻译成
json 的 yaml 放在一个新的 JSON 键中。所以 json 应该是这样的:

```
{
   "applications":
   {
       "db": {
           "image": "postgres"
       },
       "web": {
           "image": "my-djangoapp",
           "command": "python manage.py runserver 0.0.0.0:8000",
           "volumes": [
               "/data:/data"
               ],
           "ports": [
               "8000:8000"
               ],
           "links": [
               "db"
               ]
       }
   }
}
```

这样，我可以对特定的厨师食谱使用特定的键，用于
实例标签、route53、nfs…

最后一件事。由于 OpsWorks 堆栈支持 EC2
实例的“层”,如果我能为每一层指定应用程序 json 的
键就好了。

```
{
   "layers": {
       "djangoapp":{
           "applications":
           {
               "db": {
                   "image": "postgres"
               },
                   "web": {
                       "image": "my-djangoapp",
                       "command": "python manage.py runserver 0.0.0.0:8000",
                       "volumes": [
                           "/data:/data"
                           ],
                       "ports": [
                           "8000:8000"
                           ],
                       "links": [
                           "db"
                           ]
                   }
           }
       }
   }
}
```

在上面的例子中，chef 模板文件将从“applications”键开始，将 yaml 翻译成 OpsWorks 堆栈的“djangoapp”层中所有 EC2
实例的 json
。

我们可以将一个 docker-compose.yml 文件“翻译”到每个 OpsWorks 堆栈的
层，在“层”json 层中创建更多的键。此外，我们还可以
创建一个“全局”的“应用”键，该键将被合并到
特定层的“应用”中。在这种情况下，更长的 json 应该是这样的:

```
{
   "applications":{
       "postfix" {
           "image": "postfix"
       }
   },
       "layers": {
           "djangoapp":{
               "applications":
               {
                   "db": {
                       "image": "postgres"
                   },
                   "web": {
                       "image": "my-djangoapp",
                       "command": "python manage.py runserver 0.0.0.0:8000",
                       "volumes": [
                           "/data:/data"
                           ],
                       "ports": [
                           "8000:8000"
                           ],
                       "links": [
                           "db"
                           ]
                   }
               }
           }
       }
}
```

有了上面的 json，OpsWorks 堆栈上的每个 EC2 实例都会运行容器后缀，只有名为“djangoapp”的层上的 EC2 实例会运行 postgres，而
会运行 my-djangoapp 图像。

这个“特性”还扩展到了我
提到的其他方法(更改标签、route53 条目、nfs 挂载…)，所以 OpsWorks 堆栈上真正
完整的定制 JSON 应该是这样的:

```
{
   "tags"{
       "Product": "Django example"
   },
   "applications":{
       "postfix" {
           "image": "postfix"
       }
   },
   "layers": {
       "djangoapp":{
           "tags": {
               "Service": "the application"
           },
           "applications":
           {
               "db": {
                   "image": "postgres"
               },
               "web": {
                   "image": "my-djangoapp",
                   "command": "python manage.py runserver 0.0.0.0:8000",
                   "volumes": [
                       "/data:/data"
                       ],
                   "ports": [
                       "8000:8000"
                       ],
                   "links": [
                       "db"
                       ]
               }
           }
       }
   }
}
```

我将很快准备一个 Youtube 视频，一步一步地描述这个过程，为那些没有 OpsWorks 和厨师知识的人提供帮助。

与此同时，Thiago Nache 正在提供我们写在他的 bitbucket 上的 docker-compose 食谱。你还可以在[矿](https://bitbucket.org/fbueno/opsworks-recipes)上找到一个工作叉。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！