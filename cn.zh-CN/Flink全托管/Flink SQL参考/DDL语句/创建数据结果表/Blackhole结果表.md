---
keyword: [Blackhole, Blackhole结果表]
---

# Blackhole结果表

本文为您介绍Blackhole结果表的DDL定义、WITH参数和代码示例。

## 什么是Blackhole结果表

Blackhole结果表是系统内置的Connector。如果您在注册其他类型的Connector结果表时报错，但您不确定是系统问题还是结果表WITH参数错误，您可以将WITH参数修改为'connector' = 'blackhole'后，单击**运行**。如果不再报错，则证明系统没有问题，您需要确认修改WITH参数。

## 使用限制

仅Flink计算引擎VVR 2.0.0及以上版本支持Blackhole Connector。

## DDL定义

```
create table blackhole_sink(
  name VARCHAR,
  score BIGINT
) with (
  'connector' = 'blackhole'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型。|是|固定值为blackhole。|

## 代码示例

```
CREATE TEMPORARY table datahub_source(
  name VARCHAR
) with (
  'connector'='datahub',
  'endpoint'='<yourEndpoint>',
  'project'='<yourProject>',
  'topic'='<yourTopic>',
  'subId'='<yourSubId>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>',
  'startTime'='2018-06-01 00:00:00'
);

CREATE TEMPORARY table blackhole_sink(
  name  VARCHAR  
) with (
  'connector' = 'blackhole'
);

INSERT INTO blackhole_sink
SELECT 
  LOWER(name)
from datahub_source;
```

