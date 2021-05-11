---
keyword: Phoenix5结果表
---

# Phoenix5结果表

本文为您介绍Phoenix5结果表DDL定义、WITH参数和代码示例。

**说明：** Phoenix5结果表仅支持INSERT，不支持UPDATE和DELETE。

## 什么是Phoenix5

HBase SQL服务基于Phoenix 5.x为HBase 2.x提供SQL能力，通过轻客户端即可快速连接访问。SQL服务挂载了SLB负载均衡，通过round robin模式将请求均匀分发在每个query server节点。此外轻客户端还支持Python、Go等多语言访问。Phoenix5详情请参见[HBase SQL\(Phoenix\) 5.x 使用说明]()。

## DDL定义

```
create table phoenix5_sink (
  `STATE` varchar,
  CITY varchar,
  POPULATION BIGINT,
  PRIMARY KEY (`STATE`, CITY)
) WITH (
  'connector' = 'phoenix5',
  'serverUrl' = '<yourserverUrl>',
  'tableName' = '<yourTableName>',
  'batchsize' = '25'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|默认值为`phoenix5`。|
|serverUrl|Phoenix5的Query Server地址：-   如果Phoenix5是在集群中创建的，则serverUrl是负载均衡服务的URL地址。
-   如果Phoenix5是在单机中创建的，则serverUrl是单机的URL地址。

|是|您需要在云数据库HBase实例中开启Hbase SQL服务，详情请参见[开启HBase](section_2nj_tsp_mbz)。|
|tableName|Phoenix5表名|是|无。|
|batchSize|一次批量写入的条数|否|默认值为20。|

## 代码示例

包含Phoenix5结果表的Flink作业代码示例如下。

```
CREATE TEMPORARY table datagen_source (
  `id` varchar,
  `name` varchar,
  `age` varchar,
  `birthday` varchar 
) WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY table phoenix5_sink (
  `id` varchar,
  `name` varchar,
  `age` varchar,
  `birthday` varchar,
  primary key (id)
) WITH (
  'connector' = 'phoenix5',
  'serverurl' = '<yourserverUrl>',
  'tablename' = '<yourTableName>',
  'batchsize' = '25'
);

INSERT INTO phoenix5_sink
  SELECT  `id` ,`name` , `age` ,`birthday` 
FROM datagen_source;
```

