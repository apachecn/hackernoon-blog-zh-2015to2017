# 平行扫描和滚动弹性搜索索引

> 原文：<https://medium.com/hackernoon/parallel-scan-scroll-an-elasticsearch-index-db02583d10d1>

> 编辑: [/u/warkolm](https://www.reddit.com/user/warkolm) 告诉我【elasticsearch 为我的问题提供了一个名为[切片卷轴](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/search-request-scroll.html#sliced-scroll)的原生解决方案。这个特性是在 Elasticsearch 5.0 中引入的。如果你有 es < 5.0，你仍然可以使用我在这里提出的路由解决方案；我希望其他信息对您也有帮助。

Elasticsearch 提供了一个本地 api 来扫描和滚动索引。这意味着你得到一个“光标”,你可以在上面滚动。您可以使用 [scan](http://elasticsearch-py.readthedocs.io/en/master/helpers.html#elasticsearch.helpers.scan) helper 方法来更轻松地使用 scroll api:

这个动作的缺点是它限制你只能滚动一次。要获得下一批文档，您需要下一个 scroll_id，它将在下一个 scroll 命令中获得。

扫描大型索引(数百万++文档)可能需要一段时间。如何在合理的时间内滚动包含数百万(或更多)文档的大型索引？解决方案是并行扫描。

## 弹性研究中的并行性

在 Elasticsearch 中，每个索引被分割成更小的元素，称为 [**碎片**](https://www.elastic.co/guide/en/elasticsearch/reference/5.0/glossary.html#glossary-shard) 。这些碎片分布在多个节点上。您可以定义主碎片的数量和副本的数量，以确保主碎片出现故障时的数据完整性，并提高性能—副本碎片可以处理搜索请求。

[**elastic search——Hadoop**](https://www.elastic.co/guide/en/elasticsearch/hadoop/current/arch.html)使用分片进行并行。要使用 Elasticsearch-hadoop，您需要 Spark 或 hadoop 集群，这可能会有很高的使用开销，尤其是如果您的任务非常简单。我们将尝试自己实现一种并行扫描的方法。

elasticsearch 索引中的默认碎片数是 5；这意味着 elasticsearch-hadoop 可以将扫描并行到多达 5 个并行任务。[确定](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html)创建索引时的碎片数量，以提高大型索引的并行性。

## 碎片 Api

如何自己实现并行扫描？我们不想扫描整个索引，而是想单独滚动每个片段。

> search shards api 返回搜索请求将被执行的碎片( [link](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-shards.html) )

要获取碎片和节点信息，请使用:

```
$ curl -XGET [http://elasticsearch:9200/index/_search_shards](https://esprod.senexx.com/linkedin-profiles-2017-01-23/_search_shards)
```

**routing** 参数决定请求将针对哪个碎片执行。

当我们用 routing 参数查询 search shards api 时，结果将是请求将被执行的碎片。

例如，对路由为`hello`的索引为`my-index`的搜索请求将针对碎片号 1 执行:

```
$ **curl** [**http://elasticsearch:9200/my-index/_search_shards?routing=hello**](https://esprod.senexx.com/linkedin-profiles-2017-01-23/_search_shards?routing=hello){
  "nodes": {
    ...
  },
  "shards": [
    [
      {
        "state": "STARTED",
        "primary": true,
        "node": "nu81I57KRCWw8zq27fKd1A",
        "shard": 1,
        "index": "index",
      }
    ]
  ]
}
```

要找到路由到 shard 的完整映射，我们可以使用以下脚本:

结果将是这样的:

```
{0: 491, 83: 493, 27: 465, 74: 403, 111: 508, 57: 404, 78: 495, 106: 463... }
```

这意味着如果你使用路由 **491** 的话，预期的分片将是零号分片。

每个碎片保存着大约`total documents / number of shards`的文件。我们可以通过发送路由 491 的搜索请求来验证这一点。在我的例子中，shard zero 获得了大约 300 万次点击，整个索引中总共有 3.56 亿个文档，也就是大约 120 个碎片(实际的碎片数)。

## 实施并行扫描

我们现在有了分片到路由的映射。下一步是扫描和滚动每个碎片:

我们设法将扫描/滚动分割为每个分片，但上述方法仍然是同步的；每个碎片扫描必须在另一个碎片扫描开始之前完成。让我们使用[多重处理。Pool](https://docs.python.org/2/library/multiprocessing.html#using-a-pool-of-workers) 为本地机器上的每个 CPU 运行一个 worker(碎片扫描):

我使用这个池在一台 4 CPUs 的机器上计算 5 个碎片索引中每个碎片上的文档数量(并添加了日志)。该池将工作线程的数量限制为 CPU 的数量，即 4 个并行任务。

您可以看到，只有在第一个工人完成(*)之后，最后一个工人才开始(**)。

```
[16:42:26.485186] Starting: ForkPoolWorker-1 Pid: 11447
[16:42:26.485286] Starting: ForkPoolWorker-2 Pid: 11448
[16:42:26.485437] Starting: ForkPoolWorker-3 Pid: 11449
[16:42:26.485457] Starting: ForkPoolWorker-4 Pid: 11450[16:42:28.235941] Name: ForkPoolWorker-4 Pid: 11450 Result: 3==149
[16:42:28.264004] Name: ForkPoolWorker-3 Pid: 11449 Result: 2==152
[16:42:28.581638] Name: ForkPoolWorker-1 Pid: 11447 Result: 0==170
[16:42:29.845960] Name: ForkPoolWorker-2 Pid: 11448 Result: 1==133*  [16:42:33.240230] Exiting: ForkPoolWorker-4 Pid: 11450
**** [16:42:33.240581] Starting: ForkPoolWorker-4 Pid: 11450**
[16:42:33.264458] Exiting: ForkPoolWorker-3 Pid: 11449
[16:42:33.586739] Exiting: ForkPoolWorker-1 Pid: 11447
[16:42:34.850162] Exiting: ForkPoolWorker-2 Pid: 11448
**[16:42:34.851107] Name: ForkPoolWorker-4 Pid: 11450 Result: 5==148**
**[16:42:39.854437] Exiting: ForkPoolWorker-4 Pid: 11450**
```

## 码头工人

下一步是将工人分配到一组机器上。我将每个工人打包成一个 docker 映像，并在一个[机群](https://github.com/coreos/fleet)中运行它。

要运行单个 docker，您可以使用:

```
$ **docker run -ti -e ES_HOST=*** -e ES_PORT=*** -e INDEX=index -e DOC_TYPE=doc_type -e SHARD=1 es-parallel**
```

每个工人将获得一个分片号，确定相关的路由号，运行 scan_shard 命令并退出。

worker 代码类似于前面的 sync 和 pool 代码，但是它只运行一次分片扫描。你可以在这里看到工人[的最终结果](https://github.com/amityo/es-parallel-scan/blob/master/worker.py):

## 机群并行

`es-parallel@.service`是在车队集群上启动的示例单元文件。设置正确的配置，让我们从以下内容开始:

```
$ **fleetctl start es-parallel@{0..4}** 
```

其中 4 是碎片的数量(减 1)。

注意，`ExecStart`命令使用单元号作为分片号:

```
ExecStart=/bin/sh -c '\
    /usr/bin/docker run \
        --name %p-%i \
        -e ES_HOST="" \
        -e ES_PORT="" \
        -e INDEX="" \
        -e DOC_TYPE="" \
        -e ROUTING="**%i**" \
        es-parallel '
```

结果是:

```
$ **fleetctl journal es-parallel@1**$ g-t-5 docker[43117]: Using default tag: latest
$ g-t-5 docker[43117]: latest: Pulling from es-parallel
$ g-t-5 systemd[1]: Started Es Parallel.
$ g-t-5 sh[43160]: Namespace(doc_type='doc_type', es_auth=None, es_host='elasticsearch', es_port=9200, es_use_ssl=False, index='index', shard='1')
$ g-t-5 sh[43160]: **Result: 148**
$ g-t-5 docker[43312]: es-parallel-1
```

笔记和想法:

*   当前—结果打印到控制台。你可以对他们为所欲为。
*   所有工人将同时开始。确保你的 elasticsearch 集群可以处理它。如果没有，您可以编写一个脚本，根据需要启动 workers。

## 切片卷轴

Elasticsearch 5.0 [引入了](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/search-request-scroll.html#sliced-scroll)的`sliced scroll`功能——一种将卷轴分割成多个切片的原生方式:

```
GET /index/doc_type/_search?scroll=1m
{
    "slice": {
        "id": 0, 
        "max": 2 
    },
   "query": { ... }
}GET /index/doc_type/_search?scroll=1m
{
    "slice": {
        "id": 1,
        "max": 2
    },
    "query": { ... }
}
```

`max`是切片数，`id`是切片数。最大值可以等于碎片的数量，更低或更高。首先在碎片上进行分割，然后在每个碎片上进行局部分割。这意味着如果`max == num_of_shards`每个切片将是一个单独碎片上的滚动。

请注意，当切片数量大于碎片数量时，会发生内存开销操作。

您可以使用切片滚动进行[并行重新索引](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-reindex.html#docs-reindex-automatic-slice)、[按查询更新](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update-by-query.html#docs-update-by-query-automatic-slice)和[按查询删除](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-delete-by-query.html#docs-delete-by-query-automatic-slice)。点击阅读更多[。](https://stackoverflow.com/a/43316335/1626280)

## 摘要

es-parallel 可以很好地替代在 elasticsearch 上使用 Spark/Hadoop 集群的开销。它非常适合映射、过滤和选择文档，但是你(仍然)没有办法减少结果。

在这里找到源代码[，在这里](https://github.com/amityo/es-parallel-scan)找到 docker