---
keyword: Phoenix5结果表
---

# Phoenix5结果表

本文为您介绍Phoenix5结果表DDL定义、WITH参数和代码示例。

**说明：** Phoenix5结果表仅支持INSERT，不支持UPDATE和DELETE。

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

|是|您需要在云数据库HBase实例中开启Hbase SQL服务，详情请参见[开启HBase SQL服务](t1856926.md#)。|
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

