# 1 基础入门

## 1.1 安装并运行

### 1.1.1 安装ElasticSearch

[Download Elasticsearch](https://www.elastic.co/cn/downloads/elasticsearch)，解压到合适目录，运行：

```bash
cd elasticsearch-<version>
./bin/elasticsearch
```

- 如果想把 Elasticsearch 作为一个守护进程在后台运行，那么可以在后面添加参数 `-d` 。
- 如果你是在 Windows 上面运行 Elasticseach，应该运行 `bin\elasticsearch.bat` 而不是 `bin\elasticsearch` 。

测试 Elasticsearch 是否启动成功，可以打开另一个终端，执行以下操作：

```bash
curl 'http://localhost:9200/?pretty'
```

得到如下输出：

```json
{
  "name" : "C02Z56E0LVCF",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "YLGTcu30SDeDYDXPOqSy-g",
  "version" : {
    "number" : "7.12.1",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "3186837139b9c6b6d23c3200870651f10d3343b7",
    "build_date" : "2021-04-20T20:56:39.040728659Z",
    "build_snapshot" : false,
    "lucene_version" : "8.8.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

### 1.1.2 安装Kibana

[Download Kibana](https://www.elastic.co/cn/kibana)，解压到合适目录，运行：

```bash
cd kibana-<version>
./bin/kibana
```

浏览器中打开 `http://localhost:5601`

## 1.2 和ElasticSearch交互

### 1.2.1 Java API

如果使用 Java，在代码中可以使用 Elasticsearch 内置的两个客户端：

- **节点客户端（Node client）**

  节点客户端作为一个非数据节点加入到本地集群中。换句话说，它本身不保存任何数据，但是它知道数据在集群中的哪个节点中，并且可以把请求转发到正确的节点。

- **传输客户端（Transport client）**

  轻量级的传输客户端可以将请求发送到远程集群。它本身不加入集群，但是它可以将请求转发到集群中的一个节点上。

两个 Java 客户端都是通过 *9300* 端口并使用 Elasticsearch 的原生传输协议和集群交互。集群中的节点通过端口 9300 彼此通信。如果这个端口没有打开，节点将无法形成一个集群。

> Java 客户端作为节点必须和 Elasticsearch 有相同的 ***主要*** 版本；否则，它们之间将无法互相理解。

### 1.2.2  RESTful API with JSON over HTTP

所有其他语言可以使用 RESTful API，通过端口 *9200* 和 Elasticsearch 进行通信，另外可以用 web 客户端访问 Elasticsearch ，甚至可以使用 `curl` 命令来和 Elasticsearch 交互。

一个 Elasticsearch 请求和任何 HTTP 请求一样由若干相同的部件组成：

```bash
curl -X<VERB> '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'
```

被 `< >` 标记的部件：

| 标记           | 含义                                                         |
| -------------- | ------------------------------------------------------------ |
| `VERB`         | 适当的 HTTP *方法* 或 *谓词* : `GET`、 `POST`、 `PUT`、 `HEAD` 或者 `DELETE`。 |
| `PROTOCOL`     | `http` 或者 `https`（如果你在 Elasticsearch 前面有一个 `https` 代理） |
| `HOST`         | Elasticsearch 集群中任意节点的主机名，或者用 `localhost` 代表本地机器上的节点。 |
| `PORT`         | 运行 Elasticsearch HTTP 服务的端口号，默认是 `9200` 。       |
| `PATH`         | API 的终端路径（例如 `_count` 将返回集群中文档数量）。Path 可能包含多个组件，例如：`_cluster/stats` 和 `_nodes/stats/jvm` 。 |
| `QUERY_STRING` | 任意可选的查询字符串参数 (例如 `?pretty` 将格式化地输出 JSON 返回值，使其更容易阅读) |
| `BODY`         | 一个 JSON 格式的请求体 (如果请求需要的话)                    |

例如，计算集群中文档的数量，我们可以用这个:

```bash
curl -XGET 'http://localhost:9200/_count?pretty' -d 
'{
    "query": {
        "match_all": {}
    }
}'
```

Elasticsearch 返回一个 HTTP 状态码（例如：`200 OK`）和（除`HEAD`请求）一个 JSON 格式的返回值。前面的 `curl` 请求将返回一个像下面一样的 JSON 体：

```json
{
    "count" : 0,
    "_shards" : {
        "total" : 5,
        "successful" : 5,
        "failed" : 0
    }
}
```

在返回结果中没有看到 HTTP 头信息是因为我们没有要求`curl`显示它们。想要看到头信息，需要结合 `-i` 参数来使用 `curl` 命令：

```bash
curl -i -XGET 'localhost:9200/'
```

## 1.3 ElasticSearch基本概念

- 索引（Index）：类似关系型数据库（RDB）中的database
- 类型（Type）：类似RDB中的table
- 文档（Document）：类似table中的一行记录



# 2 深入搜索





> Reference:
>
> - [《Elasticsearch: 权威指南》](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)

