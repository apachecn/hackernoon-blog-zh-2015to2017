# 为什么我放弃了我几乎完成的项目

> 原文：<https://medium.com/hackernoon/why-i-give-up-my-almost-done-project-a94114eb7b15>

作者:[西尔弗-约翰·伊姆霍夫](https://medium.com/u/85a8e65ba563?source=post_page-----a94114eb7b15--------------------------------)，[马修·莫兰维尔](https://medium.com/u/9af9bace60e5?source=post_page-----a94114eb7b15--------------------------------)

![](img/af80a09637aff858f78fb567484c2855.png)

W 当我们完成最后一个使用 [Graphcool](https://www.graph.cool/) 作为后端的项目 [Freatle](https://freatle.com/) 时，我们意识到没有 MySQL 可以替代这个真正酷的 [BaaS](https://de.wikipedia.org/wiki/Backend_as_a_Service) 。所以我们想知道我们是否可以做一些类似的事情，但是我们不依赖于 GraphQL，而是创建一些 REST 端点和一个 MySQL 数据库。无贫的想法由此诞生。

## **为什么这看起来是个好主意**

当你从零开始一个项目，你需要一个数据库，你必须在本地安装一个服务器，比如在 Windows 上安装带有 Xampp 的 MariaDB。当你在构建一个附带项目时，这是浪费时间。使用无服务器解决方案，您只需调用 API 来管理您的数据。

## **我如何打造 pooless**

作为 Poorless 应该是一个 API，只有一个好的选择:Go。使用小而好的框架 [Echo](https://echo.labstack.com/) ，你可以在不到 1 天的时间内构建一个小的 API。这个计划非常简单:

*   一个用户需要管理一些数据，所以他去了穷人网站，
*   他填写表单，通过输入他想要创建的实体及其属性来创建他的数据库和表，
*   API 接收来自用户的请求，使用 UUID 创建数据库和表，
*   API 将 UUID 返回给用户，然后用户可以访问他的私有数据库。

```
 e.GET("/:key/table/:table", handler.GetTable)
  e.GET("/:key/table/:table/:id", handler.GetRowById)    
  e.GET("/:key/table/:table/:filter/:id", handler.GetFilteredRow) 
  e.POST("/:key/table/:table", handler.AddRow)
  e.PUT("/:key/table/:table/:id", handler.AddRow)  
  e.DELETE("/:key/table/:table", handler.DeleteTable)
  e.DELETE("/:key/table/:table/:id", handler.DeleteTable)
  e.DELETE("/:key/table/:table/:filter/:id", handler.DeleteTable)
```

在 API 端，我们有一个 MySQL 服务器在运行，我们在那里存储用户的数据。用 Go 访问和管理 MySQL 服务器真的很简单。

```
*/**
 *Here we create the user's database*
**/*
**func** CreateDatabase(db *****sql.DB, uid **string**) {
  log.Println("Create Database " **+** uid)
  _, err **:=** db.Query("Create Database " **+** uid)
  **if** err **!=** **nil** {
    log.Fatal(err)
  }
}
```

## **穷人的极限**

正如您在上面看到的，我们创建了用户可以调用来管理其数据的路由。因为这是一个 REST API，所以一个路由=一个实体。所以这条路线

```
e.GET("/:key/table/:table", handler.GetTable)
```

经办人

```
**func** GetTable(c echo.Context) **error** {  
  userId **:=** c.Param("key")
  table **:=** c.Param("table")

  config, configErr **:=** util.GetConfig("./config.json")
	**if** configErr **!=** **nil** {
		log.Fatal(configErr)
	}

  db **:=** service.GetDatabaseConnexion(config, userId)
  data **:=** service.FindAll(db, table)

  **return** c.JSON(http.StatusOK, data)
}
```

与...匹配

```
*/**
 *Here we find all row in the table*
**/*
**func** FindAll(db *****sql.DB, tableName **string**) *****sql.Rows{
  query **:=** "Select * From " **+** tableName
  log.Print(query)
  rows, err **:=** db.Query(query)
  **if** err **!=** **nil** {
    log.Fatal(err)
  }
  **return** rows
}
```

你看出问题了吗？你被限制了。

如果只需要选择一些字段，添加一个 count，一个 groupby 等。你不能。如果你使用 Poorless，你会被限制在我们创建的路线上。所以我问自己:

> 如何让用户自定义请求，而不必编写更多的路由？

解决方案已经存在，我很喜欢它:GraphQL。

使用 GraphQL，作为一个客户端，您告诉 API 您需要什么。只需一个请求，您就可以检索许多实体，添加任意多过滤器，等等。

## **该不该用 GraphQL？**

是的。

> 我不相信。

好好读读[这个](https://codeburst.io/test-is-graphql-right-for-you-21a7a4858769)和[这个](https://hackernoon.com/how-to-build-a-good-side-project-5ae8ecf3a6d8)。:)

## 害怕放弃

假设你想开发一个应用程序，你雇佣了一些移动开发者，他们每月花费你 10，000 美元。五个月已经过去了，你还需要两个月来结束这个项目。但是你的想法在项目开始的时候就泄露了，而你并不知道。有人偷了它并发布了应用程序。你应该投资 20，000 美元来结束你的项目以发布一个已经存在的应用吗？这叫做[沉没成本](https://en.wikipedia.org/wiki/Sunk_cost)。在这种情况下，你应该停止应用程序的生产。你的钱已经花完了，两个月后发布应用只会让你浪费更多的钱。

> 那么我是不是白干了？

你当然没有。即使你没有达到你的目标，即使你没有意识到它，你已经学到了一些东西。放弃你的项目不会从你的大脑中移除这些知识和经验。