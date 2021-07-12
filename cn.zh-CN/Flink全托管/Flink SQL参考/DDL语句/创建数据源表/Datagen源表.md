---
keyword: [Datagen, Datagen源表]
---

# Datagen源表

本文为您介绍Datagen源表的DDL定义、WITH参数和代码示例。

## 什么是Datagen源表

Datagen源表是系统内置的Connector，可以周期性地生成Datagen源表对应类型的随机数据。如果您在注册其他类型的Connector源表时报错，但您不确定是Flink系统问题还是源表WITH参数错误，您可以将WITH参数修改为'connector' = 'datagen'后，单击**运行**。如果不再报错，则证明Flink系统没有问题，您需要确认修改WITH参数。

## 使用限制

仅VVR 2.0.0及以上版本支持Datagen Connector。

## DDL定义

```
CREATE TABLE datagen_source (
  name VARCHAR,
  score BIGINT
) WITH (
  'connector' = 'datagen'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|源表类型。|是|固定值为datagen。|
|rows-per-second|生成随机数据的速率。|否|默认值为10000条/秒。|
|fields.\#.length|生成随机字符串数据的长度。|否|默认值为100。支持CHAR、VARCHAR和STRING。|
|fields.\#.kind|-   在数据源是无界的，即fields.\#.kind为随机生成器（默认值）。
-   在数据源是有界的，即fields.\#.kind为序列生成器。

|否|无。|
|fields.\#.max|随机发生器的最大值。|否|仅支持数字类型。|
|fields.\#.min|随机发生器的最小值。|否|仅支持数字类型。|
|fields.\#.start|序列发生器的起始值。|否|无。|
|fields.\#.end|序列发生器的结束值。|否|无。|

## 代码示例

```
CREATE TEMPORARY table datahub_source(
  name VARCHAR
) WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY table datahub_sink(
  name  VARCHAR  
) WITH (
  'connector'='datahub',
  'endpoint'='<yourEndpoint>',
  'project'='<yourProject>',
  'topic'='<yourTopic>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>'
);

INSERT INTO datahub_sink
SELECT 
  LOWER(name)
from datahub_source;
```

