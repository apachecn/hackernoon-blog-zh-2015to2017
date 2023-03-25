# 速度快 3 倍的烧瓶应用

> 原文：<https://medium.com/hackernoon/3x-faster-than-flask-8e89bfbe8e4f>

## 夸脱作为烧瓶的升级

自从大约 8 年前第一次发布 [Flask](https://github.com/pallets/flask) 以来，Python 一直在发展，特别是随着 asyncio 的引入。Asyncio 允许开发诸如 [uvloop](https://github.com/MagicStack/uvloop) 和 [asyncpg](https://github.com/magicstack/asyncpg) 之类的库，据报道([这里是](https://magic.io/blog/uvloop-blazing-fast-python-networking/)，这里是[这里是](https://magic.io/blog/asyncpg-1m-rows-from-postgres-to-python/))，以提高性能，远远超过以前可能的性能。遗憾的是，Flask 不容易与 asyncio 或这些库结合。然而，Flask-API 可以通过 [Quart](https://gitlab.com/pgjones/quart) 框架与 asyncio 一起使用。

Quart 为 Flask 应用程序使用 asyncio 提供了最简单的过渡，因为它共享 Flask-API。这意味着现有的 Flask 应用程序可以毫不费力地发展成 Quart 应用程序，然后使用这些新的库来利用 Flask 无法实现的性能改进。

本文档详细介绍了典型生产 [CRUD](https://en.wikipedia.org/wiki/Create%2C_read%2C_update_and_delete) 应用从烧瓶到夸脱的转换，并展示了典型生产部署的性能改进。

## TL；博士；医生

将这款 Flask-pyscopg2 应用升级到 Quart-asyncpg 应用，性能提升了 3 倍，而无需对代码进行重大重写或调整。总结结果，

```
Route           | Flask | Quart | Ratio 
                | Req/s | Req/s | (Quart/Flask)
-----------------------------------------------
GET /films/995/ |   330 |  1160 |          3.5
GET /films/     |    99 |   195 |          2.0
POST /reviews/  |   325 |  1114 |          3.4
```

# 该应用程序

为了进行比较，我将考虑一个简单的应用程序，它简单地提供了一个 RESTful 数据库接口。这是微服务架构中的一个常见用例，提供了一个非常简单的代码库进行比较。

该应用程序有两个[蓝图](http://flask.pocoo.org/docs/0.12/blueprints/)，总共包括三条路线。这些路线旨在代表典型的 CRUD 使用，即`GET /films/<int:id>/`单个资源、`GET /films/`所有资源、`POST /reviews/`新资源。

源代码可以在 https://github.com/pgjones/faster_than_flask_article[获得](https://github.com/pgjones/faster_than_flask_article)注意有两个提交，一个 Flask 版本和一个 Quart 版本。

## 从烧瓶到夸脱的演变

从 flask 到 quart 的演变意味着很容易并且需要最小的改变，特别是从 Flask 的导入被改变为从 Quart，函数变成异步的。全[差](https://github.com/pgjones/faster_than_flask_article/commit/0a70f2bddae90da13da5bce2b77ea56355ecc5d1)的一个例子是，

```
def add_review():
    data = request.get_json()
    ...
```

这就变成了，

```
async def add_review():
    data = await request.get_json()
    ...
```

## 从 psycopg2 到 asyncpg 的演变

发展 psycopg2 代码以使用 asyncpg 要稍微复杂一些，因为两者有不同的用法。为了简化差异，Flask 应用程序中使用了一个`PoolWrapper`来提供一个上下文管理的 psycopg2 连接，该连接使用与 asyncpg 相同的 API，即`with pool.acquire() as connection:`。这允许通过将`with`更改为`async with`来使用 asyncpg。

除了连接之外，Asyncpg 和 psycopg2 在游标使用、事务、执行参数和查询格式上也有所不同。这些大多是不同的约定，细节最好在 [diff](https://github.com/pgjones/faster_than_flask_article/commit/0a70f2bddae90da13da5bce2b77ea56355ecc5d1) 中考虑。

## 部署

将 Flask 应用程序直接暴露给生产流量不太可能扩展，也不能真正代表典型的生产环境。这是因为 Flask 本身一次只能处理一个请求。相反，WSGI 服务器通常与某种异步工作器结合使用，例如 [Gunicorn](https://github.com/benoitc/gunicorn) 和 [eventlet](https://github.com/eventlet/eventlet) 。

Quart 最好与 Gunicorn 一起部署，它允许使用相同的命令来运行 Flask 和 Quart 应用程序，

```
$ gunicorn --config gunicorn.py 'run:create_app()'
```

配置文件[与](https://github.com/pgjones/faster_than_flask_article/commit/0a70f2bddae90da13da5bce2b77ea56355ecc5d1#diff-e61b1f0efe946725406491d0bb56c68b)的区别仅在于使用了哪个 worker(event let 用于 Flask，Quart-UVLoop 用于 Quart)。

性能测量是在 Gunicorn 后面运行 Flask 和 Quart 应用程序的情况下进行的。

## 数据库ˌ资料库

Postgresql [示例数据库](http://www.postgresqltutorial.com/postgresql-sample-database/)用于以一种粗鲁的方式给应用程序一些东西来对抗。除了添加一个简单的回顾表之外，

```
CREATE TABLE review (
    film_id INTEGER REFERENCES film(film_id), 
    rating INTEGER
);
```

# 性能测定

使用 wrk 来测量应用程序[的性能。它被配置为使用 20 个连接来匹配数据库连接池的大小(这应该确保最高的吞吐量，20 是我使用的典型值)。为了测量 GET 请求，命令是:](https://github.com/wg/wrk)

```
$ wrk --connections 20 --duration 5m http://localhost:5000/${PATH}/
```

而对于 POST 请求，命令是，

```
$ wrk --connections 20 --duration 5m --script post.lua http://localhost:5000/${PATH}/
```

用 post.lua 文件定义[这里](https://github.com/pgjones/faster_than_flask_article/blob/master/post.lua)。

## 测试系统

数据库 Postgres (9.5.10)、wrk (4.0.0)和 apps Python (3.6.3)与需求 asyncpg (0.13.0)、Flask (0.12.2)、Gunicorn (19.7.1)、psycopg2 (2.7.3.2)、Quart (0.3.1)都运行在一台 AWS c4.large 机器上。

# 结果

全部结果如下表所示，

```
Route           | Requests per second | Average Latency [ms]  |
                |   Flask  |   Quart  |   Flask   |   Quart   |
---------------------------------------------------------------
GET /films/995/ |   330.22 |  1160.27 |     60.55 |     17.23 |
GET /films/     |    99.39 |   194.58 |    201.14 |    102.76 |
POST /reviews/  |   324.49 |  1113.81 |     61.60 |     18.22 |
```

请注意，四分之一服务器的每秒请求数增加了 2 到 3.5 倍，平均延迟相应减少了 2 到 3.5 倍。

# 结论

从 Flask 应用程序到 Quart 应用程序的演变相当容易，因为共享 API 主要意味着在正确的位置编写`async` & `await`的工作。然而，如果使用 SQLAlchemy(或其他 ORM ),从 psycopg2 到 asyncpg 的演变会更加复杂，可能会更麻烦。

应用程序的性能明显提高。这一改进主要归功于 Quart 启用的 asyncpg 和 uvloop 的使用。据估计，仅 Quart 一项就能提供 1.5 倍的速度提升。

总之，从 Flask-psycopg2 应用程序到 Quart-asyncpg 应用程序的过渡是相当容易的，并且给出了非常合理的性能改进。这可能会扩展到任何其他基于 asyncio 的库，这意味着 Quart 可以让 Flask 应用程序轻松过渡到 asyncio 生态系统。