# ElasticSearch初识

## 什么是elasticsearch

**Elasticsearch** 是一个基于 **分布式架构** 的开源搜索引擎。它能够**提供强大的搜索、数据存储和分析功能**，适用于海量数据的关键词搜索和分析场景。

## 正向索引与倒排索引

elasticsearch 采用倒排索引

正向索引就是通过对文档的标识也就是id建立的索引，而倒排索引是对文档内容的关键词建立的索引，会根据关键词查询到文档的id

对于关键词搜索的场景，倒排索引检索效率很高，因为不需要遍历所有文档内容就可以找到包含该关键词的文档列表，而正排索引不仅要遍历文档数据，还有在每条数据中进行模糊查询。

## 索引、文档、映射

![在这里插入图片描述](D:\E\学习\Go\笔记\技术栈\数据库\assets\5afaec02fe6279fdf06e6b0f822841ae.png)

# 基本操作

## 索引操作

### 查询索引

查询所有索引

```http
GET /_cat/indices?v
```

查询指定索引

```http
GET /my-index
```

### 创建索引

```http
PUT /my-index
```

在创建索引时还可以指定以下内容：

- 索引的设置（setting）
- 索引中字段的映射（mapping）
- 索引别名（alias）

例如

```json
PUT /review-1
{
  "settings": {
     //分片数和副本数
    "number_of_shards": 3,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "id":{
        "type": "long"
      },
      "userID":{
        "type": "long"
      },
      "score":{
        "type": "integer"
      },
      "status":{
        "type": "integer"
      },
      "content":{
        "type": "text"
      },
      "publishTime":{
        "type": "date"
      },
      "tags":{
        "type": "nested",
        "properties": {
          "code":{
            "type": "keyword"
          },
          "title":{
            "type": "text"
          }
        }
      }
    }
  },
  "aliases": {
    "review_1": {}
  }
}
```

### 删除索引

```http
DELETE /my-index
```

### 修改索引

索引库和 mapping 一旦创建无法修改，但是可以添加新的字段，语法如下：

```
添加字段： PUT / 索引库名 /_mapping
{
  "properties": {
    "age": {
      "type": "integer"
    }
  }
}
```

## 文档操作

### 创建

```json
POST /索引库名/_doc/id
POST /iam/_doc/2
{
  "info": "Java工程师",
  "email": "zy@itcast.cn",
  "age": 20,
  "name": {
    "firstName": "张",
    "fullName": "张三"
  }
}
```

返回结果

```json
{
  "_index": "iam",
  "_id": "2",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 3,
  "_primary_term": 1
}
```

### 删除

```
DELETE /索引库名/_doc/文档 id
```

### 修改

#### 增量修改

```
POST /<index>/_update/<_id>
```

```json
POST /review-1/_update/1
{
  "doc": {
    "content": "这是修改过的好评！"
  }
}

```

### 查询

#### 获取文档

查询整个文档的内容包括元数据

```http
GET /索引库名/_doc/id
```

```json
{
  "_index": "review-1",
  "_id": "1",
  "_version": 1,
  "_seq_no": 0,
  "_primary_term": 1,
  "found": true,
  "_source": {
    "id": 1,
    "userID": 147982601,
    "score": 5,
    "status": 2,
    "publishTime": "2023-09-09T16:07:42.499144+08:00",
    "content": "这是一个好评！",
    "tags": [
      {
        "code": 1000,
        "title": "好评"
      },
      {
        "code": 2000,
        "title": "物超所值"
      },
      {
        "code": 3000,
        "title": "有图"
      }
    ]
  }
}
```

#### 获取数据

```http
GET /review-1/_source/1
```

```json
{
  "id": 1,
  "userID": 147982601,
  "score": 5,
  "status": 2,
  "publishTime": "2023-09-09T16:07:42.499144+08:00",
  "content": "这是一个好评！",
  "tags": [
    {
      "code": 1000,
      "title": "好评"
    },
    {
      "code": 2000,
      "title": "物超所值"
    },
    {
      "code": 3000,
      "title": "有图"
    }
  ]
}
```

#### 获取数据指定字段

```
GET /review-1/_source/1?_source=content,score
```

```
{
  "score": 5,
  "content": "这是一个好评！"
}
```

#### 批量获取

```json
GET /review-1/_mget
{
  "docs":[
    {
      "_id":"1"
    },
    {
      "_id":"2"
    }
  ]
}
```

### 检索

```
GET /<target>/_search
```

查询`userID=147982601`的文档。

```json
GET /review-1/_search
{
  "query": {
    "bool": {
      "filter":{
        "term":{"userID": 147982601}
      }
    }
  }
}
```

### 获取数量

用于获取搜索查询的匹配数量的请求格式如下。

```
GET /<target>/_count
```

# Go操作ElasticSearch

## 建立连接

```go
import (
	"context"
	"fmt"
	"github.com/elastic/go-elasticsearch/v8"
	"github.com/elastic/go-elasticsearch/v8/typedapi/types"
	"strconv"
	"time"
)

var es *elasticsearch.TypedClient

func initElasticSearch() {
	// ES 配置
	cfg := elasticsearch.Config{
		Addresses: []string{
			"http://localhost:9200",
		},
	}
	// 创建客户端连接
	var err error
	es, err = elasticsearch.NewTypedClient(cfg)
	if err != nil {
		fmt.Printf("elasticsearch.NewTypedClient failed, err:%v\n", err)
		return
	}
}
```



## 索引操作

### 创建索引

```go
// CreateIndex 创建索引库
func CreateIndex(indexName string) {
	// 创建索引的请求
	res, err := es.Indices.Create(indexName).Do(context.Background())
	if err != nil {
		fmt.Printf("Create index failed, err:%v\n", err)
		return
	}
	fmt.Println("创建索引：", res.Index)
}
```



## 文档操作

### 创建

```go
// CreateDocument 创建文档
func CreateDocument() {
    d1 := Review{
       ID:      1,
       UserID:  147982601,
       Score:   5,
       Content: "这是一个好评！",
       Tags: []Tag{
          {1000, "好评"},
          {1100, "物超所值"},
          {9000, "有图"},
       },
       Status:      2,
       PublishTime: time.Now(),
    }

    // 添加文档
    resp, err := es.Index("my-review").
       Id(strconv.FormatInt(d1.ID, 10)).
       Document(d1).
       Do(context.Background())
    if err != nil {
       fmt.Printf("indexing document failed, err:%v\n", err)
       return
    }
    fmt.Printf("result:%#v\n", resp.Result)
}
```

### 删除

```go
// deleteDocument 删除 document
func deleteDocument(id int64) {
    resp, err := es.Delete("my-review", strconv.FormatInt(id, 10)).
       Do(context.Background())
    if err != nil {
       fmt.Printf("delete document failed, err:%v\n", err)
       return
    }
    fmt.Printf("result:%v\n", resp.Result)
}
```

### 修改

```go
// updateDocument 更新文档
func updateDocument() {
    // 修改后的结构体变量
    d1 := Review{
       ID:      1,
       UserID:  147982601,
       Score:   5,
       Content: "这是一个修改后的好评！", // 有修改
       Tags: []Tag{ // 有修改
          {1000, "好评"},
          {9000, "有图"},
       },
       Status:      2,
       PublishTime: time.Now(),
    }
    // 更新文档
    //也可以直接使用json字符串更新
    resp, err := es.Update("my-review", "1").
       Doc(d1). // 使用结构体变量更新
       Do(context.Background())
    if err != nil {
       fmt.Printf("update document failed, err:%v\n", err)
       return
    }
    fmt.Printf("result:%v\n", resp.Result)
}
```

### 查询

#### 指定id查询

```go
// 获取文档
// getDocument 获取文档
func getDocument(id string) {
    resp, err := es.Get("my-review", id).
       Do(context.Background())
    if err != nil {
       fmt.Printf("get document by id failed, err:%v\n", err)
       return
    }
    fmt.Printf("fileds:%s\n", resp.Source_)
}
```

#### 查询所有

```go
// searchDocument 搜索所有文档
func searchDocument() {
    // 搜索文档
    resp, err := es.Search().
       Index("my-review").
       Query(&types.Query{
          MatchAll: &types.MatchAllQuery{},
       }).
       Do(context.Background())
    if err != nil {
       fmt.Printf("search document failed, err:%v\n", err)
       return
    }
    fmt.Printf("total: %d\n", resp.Hits.Total.Value)
    // 遍历所有结果
    for _, hit := range resp.Hits.Hits {
       fmt.Printf("%s\n", hit.Source_)
    }
}
```

#### 条件查询

```go
// searchDocument2 指定条件搜索文档
func searchDocument2() {
	// 搜索content中包含好评的文档
	resp, err := es.Search().
		Index("my-review").
		Query(&types.Query{
			MatchPhrase: map[string]types.MatchPhraseQuery{
				"content": {Query: "好评"},
			},
		}).
		Do(context.Background())
	if err != nil {
		fmt.Printf("search document failed, err:%v\n", err)
		return
	}
	fmt.Printf("total: %d\n", resp.Hits.Total.Value)
	// 遍历所有结果
	for _, hit := range resp.Hits.Hits {
		fmt.Printf("%s\n", hit.Source_)
	}
}
```

