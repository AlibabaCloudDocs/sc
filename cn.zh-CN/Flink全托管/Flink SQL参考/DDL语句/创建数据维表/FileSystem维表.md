---
keyword: 创建FileSystem维表
---

# FileSystem维表

本文为您介绍FileSystem维表的DDL定义，以及创建维表时使用的WITH参数和代码示例。

## 前提条件

已创建OSS存储空间，详细请参见[创建存储空间](/cn.zh-CN/快速入门/控制台快速入门/创建存储空间.md)。

## 使用限制

仅Flink计算引擎VVR 3.0.0及以上版本支持FileSystem Connector。

## DDL定义

```
CREATE TABLE filesystem_dim (
  id STRING,
  name STRING
) WITH (
  'connector' = 'filesystem',
  'path' = 'oss://my_path/my_file',
  'format' = 'csv' 
)
```

## WITH 参数

|参数|说明|是否必选|备注|
|--|--|----|--|
|connector|维表类型|是|固定值为filesystem。|
|path|文件路径|是|URI格式，例如：oss://my\_path/my\_file。|
|format|文件格式|是|参数取值如下：-   csv
-   json
-   avro
-   parquet
-   orc
-   raw

format参数详情请参见[formats](https://ci.apache.org/projects/flink/flink-docs-master/zh/docs/connectors/table/formats/overview/)。 |
|lookup.join.cache.ttl|重新读取数据的TTL时间|否|默认值为60分钟，即每隔60分钟重新读取数据。|

## 代码示例

```
--创建event源表。
CREATE TABLE event (
  id STRING, 
  data STRING
) WITH (
  'connector' = 'datahub'
   ...
);

--创建white_list维表。
CREATE TABLE white_list (
  id STRING,
  name STRING,
) WITH (
  'connector' = 'filesystem',
  'path' = '${remote_path_uri}',
  'format' = '${format}'
);

--关联event源表和white_list维表。
SELECT e.*, w.*
FROM event AS e
JOIN white_list FOR SYSTEM_TIME AS OF proctime() AS w
ON e.id = w.id;
```

