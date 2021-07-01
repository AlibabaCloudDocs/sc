---
keyword: [维表, MaxCompute]
---

# MaxCompute维表

本文为您介绍MaxCompute维表的DDL定义、WITH参数、CACHE参数、类型映射和常见问题。

## 什么是MaxCompute

大数据计算服务MaxCompute（原名ODPS）是一种快速、完全托管的EB级数据仓库解决方案，致力于批量结构化数据的存储和计算，提供海量数据仓库的解决方案及分析建模服务。MaxCompute详情请参见[什么是MaxCompute](/cn.zh-CN/产品简介/什么是MaxCompute.md)。

## 前提条件

已创建MaxCompute表，详情请参见[创建表](/cn.zh-CN/快速入门/通过MaxCompute客户端使用MaxCompute/创建表.md)。

## DDL定义

```
create table odps_dim(
  id VARCHAR,
  name VARCHAR,
  age int,
  PRIMARY KEY (id, name) not enforced
) with (
  'connector' = 'odps', 
  'endpoint' = '<yourEndpoint>',
  'tunnelEndpoint' = '<yourTunnelEndpoint>',
  'project' = '<yourProjectName>',
  'tablename' = '<yourTableName>',
  'accessid' = '<yourAccessKeyId>',
  'accesskey' = '<yourAccessKeySecret>',
  'partition' = 'ds=2018****',
  'cache' = 'ALL'
);
```

**说明：**

-   声明维表时，必须要指名主键，MaxCompute维表主键必须具有唯一性，否则会被去重。
-   在维表进行JOIN时，ON条件必须包含所有主键的等值条件。
-   不支持将MaxCompute维表分区列写入DDL定义中。
-   主键字段必须按照PRIMARY KEY括号中字段的先后顺序，放在非主键字段之前。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|维表类型。|是|固定值为`odps`。|
|endPoint|MaxCompute服务地址。|是|请参见[开通MaxCompute服务的Region和服务连接对照表](/cn.zh-CN/准备工作/配置Endpoint.md)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址。|是|请参见[开通MaxCompute服务的Region和服务连接对照表](/cn.zh-CN/准备工作/配置Endpoint.md)。|
|project|MaxCompute项目名称。|是|无。|
|tableName|表名。|是|无。|
|accessId|AccessKey ID。|是|无。|
|accessKey|AccessKey Secret。|是|无。|
|partition|分区名。|否|-   固定分区
    -   只存在一个分区MaxCompute表

例如，如果只存在1个分区列`ds`，则``partition` = 'ds=20180905'`表示读`ds=20180905`分区的数据。

    -   存在多个分区的MaxCompute表

例如，如果存在2个分区列`ds`和`hh`，则``partition`='ds=20180905,hh=*'`表示读`ds=20180905`分区的数据。

**说明：** 分区过滤时需要声明所有分区的值。例如，上述示例中，只声明``partition` = 'ds=20180905'`，则不会读取任何分区。

-   非固定分区
    -   支持``partition` = 'max_pt()'`， 即每次加载所有分区列表中字典序最大的分区。
    -   支持``partition` = 'max_pt_with_done()'`，即每次加载所有分区列表中字典序最大且伴随有`.done`的分区。 |

## CACHE参数

|参数|参数说明|备注|
|--|----|--|
|cache|缓存策略。|目前MaxCompute维表仅支持`ALL`策略，必须显式声明。 ALL策略：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查询都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新加载一遍全量Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）特别多的场景。需要配置缓存更新时间间隔（cacheTTLMs）和更新时间黑名单（cacheReloadTimeBlackList）参数。

**说明：**

-   因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的至少4倍，具体值与MaxCompute存储压缩算法有关。
-   在使用超大MaxCompute维表时，如果频繁GC（Allocation Failure）导致作业异常，且在增加维表JOIN节点的内存仍无改善的情况下，建议改为支持LRU cache策略的KV型维表，例如云数据库Hbase版维表。 |
|cacheSize|缓存大小。|可以设置缓存大小，MaxCompute默认缓存值为100000行。如果您的数据超过100000行，建议设置cacheSize参数值大于实际值，否则会报错`Partition null table count ODPS tables row count exceeds maxRowCount limit {2}`。 |
|cacheTTLMs|缓存超时时间。|单位为毫秒，如果cache选择为`ALL`策略，则为缓存加载的间隔时间，默认为不重新加载。|
|cacheReloadTimeBlackList|更新时间黑名单。在缓存策略选择为ALL时，启用更新时间黑名单，防止在此时间内做Cache更新（例如双11场景）。|默认为空，格式为`2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11 08:00`。分隔符的使用情况如下所示： -   用英文逗号（,）来分隔多个黑名单。
-   用箭头（-\>）来分割黑名单的起始结束时间。 |

## 类型映射

|MaxCompute字段类型|Flink字段类型|
|--------------|---------|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DATETIME|TIMESTAMP|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|STRING|VARCHAR|
|DECIMAL|DECIMAL|
|BINARY|VARBINARY|

## 常见问题

-   [max\_pt\(\)和max\_pt\_with\_done\(\)的区别是什么？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [endPoint和tunnelEndpoint是指什么？如果配置错误会产生什么结果？](/cn.zh-CN/Flink全托管/常见问题.md)

