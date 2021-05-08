---
keyword: [Elasticsearch, ES, 源表]
---

# 全量Elasticsearch源表

本文为您介绍全量Elasticsearch（ES）源表DDL定义、WITH参数、类型映射和代码示例。

**说明：**

-   仅支持Elasticsearch 5.5及以上版本。
-   仅支持全量Elasticsearch源表，不支持增量Elasticsearch源表。
-   DDL中的字段均对应Elasticsearch文档中的字段，不支持将文档ID写入表中。
-   Elasticsearch Connector可以作为Stream作业和Batch作业的源表使用。

## 什么是云数据库Elasticsearch

[阿里云Elasticsearch](/cn.zh-CN/产品简介/什么是阿里云Elasticsearch.md)兼容开源Elasticsearch的功能，以及Security、Machine Learning、Graph、APM等商业功能，致力于数据分析、数据搜索等场景服务。为您提供企业级权限管控、安全监控告警、自动报表生成等场景服务。

## DDL定义

```
 CREATE TABLE elasticsearch_source(
   name STRING, 
   location STRING, 
   `value` FLOAT
) WITH (
   'connector' ='elasticsearch',
   'endPoint' = '<yourEndPoint>',
   'accessId' = '<yourAccessId>',
   'accessKey' = '<yourAccessSecret>',
   'indexName' = '<yourIndexName>',
   'typeNames' = '<yourTypeName>'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|源表类型|是|固定值为`elasticsearch`。|
|endPoint|Server地址|是|例如：http://127.0.0.1:9200。|
|accessId|Elasticsearch实例的用户名|否|无|
|accessKey|Elasticsearch实例的密码|否|无|
|indexName|文档索引名称|是|无|
|typeNames|Type名称|否|默认值为`_doc`。**说明：** Elasticsearch 7.0以上版本不建议设置该参数。 |
|batchSize|每个scroll请求从Elasticsearch集群获取的最大文档数|否|默认值为2000。|
|keepScrollAliveSecs|scroll上下文保留的最长时间|否|单位为秒，默认值为3600。|

## 类型映射

Flink以JSON来解析Elasticsearch数据，详情请参见[数据类型映射关系](https://ci.apache.org/projects/flink/flink-docs-master/zh/dev/table/connectors/formats/json.html)。

## 代码示例

```
CREATE TEMPORARY TABLE elasticsearch_source (
   name STRING,
   location STRING,
   `value` FLOAT
) WITH (
   'connector' ='elasticsearch',
   'endPoint' = '<yourEndPoint>',
   'accessId' = '<yourAccessId>',
   'accessKey' = '<yourAccessSecret>',
   'indexName' = '<yourIndexName>',
   'typeNames' = '<yourTypeName>'
);

CREATE TEMPORARY TABLE blackhole_sink (
   name STRING,
   location STRING,
   `value` FLOAT
) WITH (
   'connector' ='blackhole'
);

INSERT INTO blackhole_sink
SELECT name, location, `value`
FROM elasticsearch_source;
```

