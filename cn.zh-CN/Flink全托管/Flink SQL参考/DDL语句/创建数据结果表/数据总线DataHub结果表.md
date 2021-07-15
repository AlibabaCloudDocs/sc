---
keyword: [DataHub, 结果表]
---

# 数据总线DataHub结果表

本文为您介绍数据总线DataHub结果表的DDL定义、WITH参数和类型映射。

## 什么是数据总线DataHub

[阿里云流数据处理平台DataHub](https://help.aliyun.com/document_detail/47439.html?spm=a2c4g.11174283.6.542.5e7b63efe2IHMj)是流式数据（Streaming Data）的处理平台，提供对流式数据的发布（Publish）、订阅（Subscribe）和分发功能，让您可以轻松构建基于流式数据的分析和应用。Flink支持将DataHub作为流式数据的输出。

## 前提条件

已创建DataHub的Project和Topic，详情请参见[创建Project和Topic]()。

## 使用限制

仅Flink计算引擎VVR 2.0.0及以上版本支持数据总线DataHub Connector。

## DDL定义

```
create table datahub_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT
) WITH (
  'connector'='datahub',
  'endpoint'='<endPoint>',
  'project'='<yourProjectName>',
  'topic'='<yourTopicName>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>',
  'batchCount'='500',
  'batchSize'='512000',
  'flushInterval'='5000'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型。|是|固定值为`datahub`。|
|endPoint|消费端点信息。|是|详情请参见[DataHub域名列表](https://help.aliyun.com/document_detail/158778.html?spm=a2c4g.11186623.6.547.77a91fd1eveQrC)。|
|accessId|AccessKey ID。|是|无。|
|accessKey|AccessKey Secret。|是|无。|
|project|项目名称。|是|无。|
|topic|Topic名称。|是|无。|
|retryTimeout|最大持续重试时间。|否|单位为毫秒，默认值为180000（半小时）。|
|retryInterval|重试间隔。|否|单位为毫秒，默认值为1000。|
|batchCount|每次批量写入数据的最大数据条数。|否|默认值为500。|
|batchSize|每次批量写入数据的大小。|否|默认值为512000 Byte。|
|flushInterval|缓存数据的最大超时时间。|否|单位毫秒，默认值为5000。|
|partitionBy|写入Sink节点前会根据partitionBy值进行hash，数据会流向对应的Sink节点。|否|默认值为空，随机发送。|
|hashFields|指定列名后，相同列的值会写入到同一个Shard。|否|默认值为Null，即随机写可以指明多个列值，用逗号（,）分隔。例如 `hashFields=a,b`。|

## 类型映射

DataHub和Flink字段类型对应关系如下，建议使用该对应关系时进行DDL声明。

|DataHub字段类型|Flink字段类型|
|-----------|---------|
|STRING|VARCHAR|
|TIMESTAMP|BIGINT|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INTEGER|INTEGER|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DECIMAL|DECIMAL|

## 代码示例

```
CREATE TEMPORARY table datahub_source(
  name VARCHAR
) with (
  'connector'='datahub',
  'endpoint'='<endPoint>',
  'project'='<yourProjectName>',
  'topic'='<yourTopicName>',
  'subId'='<yourSubId>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>',
  'startTime'='2018-06-01 00:00:00'
);

CREATE TEMPORARY table datahub_sink(
  name varchar
) with (
  'connector'='datahub',
  'endpoint'='<endPoint>',
  'project'='<yourProjectName>',
  'topic'='<yourTopicName>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>',
  'batchSize'='512000',
  'batchCount'='500'
);

INSERT INTO datahub_sink
SELECT 
  LOWER(name)
from datahub_source;
```

