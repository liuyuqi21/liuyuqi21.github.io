---
title: Elasticsearch 相关
date: 2019-08-21 10:45:08
tags: Elasticsearch
categories: 中间件
---
Elasticsearch 是一个分布式可扩展的实时搜索和分析引擎，建立在 Apache Lucene。

# 基础概念
Elasticsearch 是面向文档型数据库，一条数据就是一个文档。用 JSON 作为文档序列化的格式。

Mysql|Elasticsearch
-----|-------------
数据库（Database）|索引（Index）
表（table）|类型（Type）
记录（low）|文档（Document）
字段（Columns）|字段（Fields）

一个索引只放一个类型

# 倒排索引
传统的我们的检索是通过文章，逐个遍历找到对应关键词的位置。而倒排索引，是通过分词策略，形成了词和文章的映射关系表，这种词典 + 映射表即为倒排索引。

倒排索引底层实现是基于 FST。

# 使用

## 创建索引
`curl -XPUT http://localhost:9200/products?pretty` pretty 代表格式化

## 删除索引
`curl -X DELTE 'localhost:9200/weather'`

## 创建类型
```none
$ curl -H'Content-Type: application/json' -XPUT http://localhost:9200/test_index/_mapping/_doc?pretty -d'{
  "properties": {
    "title": { "type": "text", "analyzer": "ik_smart" }, 
    "description": { "type": "text", "analyzer": "ik_smart" },
    "price": { "type": "scaled_float", "scaling_factor": 100 }
  }
}'
```
- "_doc" 是索引名称
- 提交数据中的 `properties` 代表这个类型中各个字段的定义，其中 key 为字段名称，value 是字段的类型定义；
- "analyzer": `ik_smart` 代表这个字段需要使用 IK 中文分词器分词

## 创建文档
```none
$ curl -X POST 'localhost:9200/accounts/person/1' -d '
{
  "user": "李四",
  "title": "工程师",
  "desc": "系统管理"
}'
```
`/accounts/person/1`，最后的1是该条记录的 Id。它不一定是数字，任意字符串（比如abc）都可以。新增记录的时候，也可以不指定 Id，这时要改成 POST 请求。服务器返回的 json 对象里，_id 就是一个随机字符串

## 查看记录
`$ curl 'localhost:9200/accounts/person/1?pretty=true'`
返回的数据中，found字段表示查询成功，_source字段返回原始记录。果 Id 不正确，就查不到数据，found字段就是false。

## 删除记录
`$ curl -X DELETE 'localhost:9200/accounts/person/1'`

## 更新记录
更新记录就是使用 PUT 请求，重新发送一次数据。
记录的 Id 没变，但是版本（version）从1变成2，操作类型（result）从created变成updated，created字段变成false，因为这次不是新建记录。

## 返回所有记录
使用 GET 方法，直接请求/Index/Type/_search，就会返回所有记录。
`$ curl 'localhost:9200/accounts/person/_search'`
返回结果的 took字段表示该操作的耗时（单位为毫秒），timed_out字段表示是否超时，hits字段表示命中的记录，里面子字段的含义如下。
- total：返回记录数
- max_score：最高的匹配程度
- hits：返回的记录组成的数组

# 数据查询

## 全文搜索
```none
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "软件" }}
  "from": 1,
  "size": 1
}'
```
上面代码使用 match 查询，指定的匹配条件是desc字段里面包含"软件"这个词。
Elastic 默认一次返回10条结果，可以通过size字段改变这个设置。
还可以通过from字段，指定位移。

## 逻辑运算
如果有多个搜索关键词， Elastic 认为它们是 or 关系。
```none
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "软件 系统" }}
}'
```
上面代码搜索的是软件 or 系统。
如果要执行多个关键词的 and 搜索，必须使用布尔查询。
```none
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "desc": "软件" } },
        { "match": { "desc": "系统" } }
      ]
    }
  }
}'
```
## 短语搜索
```none
GET /megacorp/employee/_search
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    }
}
```
match_prase 直接搜索 rock climing 不分词

## 布尔查询
`filter`/`must`/`should`/`must_not`
- filter 和 must 与 SQL 中的 and 类似，must 下的条件会参与 **打分**，而 filter 不会。
- must_not 和 must 相反，查询的文档必需不符合此类下的条件
- should 下的条件不需要全部满足，默认情况下只需要满足 should 下的一个条件即可，也可以通过 minimum_should_match 参数来改变需要满足的个数，满足的 should 条件越多，对应的文档的打分就越高，打分高的文档排序会靠前。

## 排序
- `'sort'=>['price'=>'desc']`

## 多字段匹配查询
```none
'must' => [
    [
        'multi_match' => [
            'query'  => 'iPhone',
            'fields' => [
                'title^3',
                'long_title^2',
                'description',
            ],
        ],
    ],
],
```
- query 代表要查询的关键字，fields代表要查询的字段，^代表字段的权重

## 多字段匹配查询支持 Nested 对象
- 定义索引时使用 copy_to 参数

- "keyword" 是字符串类型的一种，告诉 Elasticsearch 不需要对这个字段做分词，通常用于邮箱、标签、属性等字段。
- scaled_float 代表一个小数位固定的浮点型字段，与 Mysql 的 decimal 类型类似，后面的 scaling_factor 用来指定小数位精度，100 就代表精确到小数点后两位。
- "nested" 代表这个字段是一个复杂对象，由下一级的 properties 字段定义这个对象的字段。