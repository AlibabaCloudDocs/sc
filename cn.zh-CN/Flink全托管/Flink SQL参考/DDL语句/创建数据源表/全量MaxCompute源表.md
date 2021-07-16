---
keyword: [源表, MaxCompute]
---

# 全量MaxCompute源表

本文为您介绍全量MaxCompute源表DDL定义、WITH参数、类型映射和常见问题。

**说明：** MaxCompute Connector可以作为Stream作业和Batch作业的源表使用。

## 什么是MaxCompute

大数据计算服务MaxCompute（原名ODPS）是一种快速、完全托管的EB级数据仓库解决方案，致力于批量结构化数据的存储和计算，提供海量数据仓库的解决方案及分析建模服务。MaxCompute详情请参见[什么是MaxCompute](/cn.zh-CN/产品简介/什么是MaxCompute.md)。

## 前提条件

已创建MaxCompute表，详情请参见[创建表](/cn.zh-CN/快速入门/通过MaxCompute客户端使用MaxCompute/创建表.md)。

## 使用限制

仅Flink计算引擎VVR 2.0.0及以上版本支持全量MaxCompute Connector。

## DDL定义

```
create table odps_source(
  id INT,
  user_name VARCHAR,
  content VARCHAR
) with (
  'connector' = 'odps', 
  'endpoint' = '<yourEndpoint>',
  'tunnelEndpoint' = '<yourTunnelEndpoint>',
  'project' = '<yourProjectName>',
  'tablename' = '<yourTableName>',
  'accessid' = '<yourAccessKeyId>',
  'accesskey' = '<yourAccessKeySecret>',
  'partition' = 'ds=2018****'
);
```

**说明：** WITH参数需要全部小写。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|源表类型。|是|固定值为`odps`。|
|endPoint|MaxCompute服务地址。|是|参见[各地域Endpoint对照表（外网连接方式）](/cn.zh-CN/准备工作/Endpoint.md)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址。|否|参见[各地域Endpoint对照表（外网连接方式）](/cn.zh-CN/准备工作/Endpoint.md)。**说明：** VPC环境下为必填。 |
|project|MaxCompute项目名称。|是|无|
|tableName|MaxCompute表名。|是|无|
|accessId|AccessKey ID。|是|无|
|accessKey|AccessKey Secret。|是|无|
|partition|分区名。|否|-   只存在一级分区的MaxCompute表

例如，如果只存在1个分区列`ds`，则``partition` = 'ds=20180905'`表示读`ds=20180905`分区的数据。

-   存在多级分区的MaxCompute表

例如，如果存在2个分区列`ds`和`hh`，则``partition`='ds=20180905,hh=*'`表示读`ds=20180905`分区的数据。

**说明：** 分区过滤时需要声明所有分区的值。例如，上述示例中，只声明``partition` = 'ds=20180905'`，则不会读取任何分区。 |

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
|DECIMAL|DECIMAL|
|BINARY|VARBINARY|
|STRING|VARCHAR|

## 代码示例

```
CREATE TEMPORARY TABLE odps_source (
  cid varchar,
  rt DOUBLE
) with (
  'connector' = 'odps', 
  'endpoint' = '<yourEndpointName>', 
  'tunnelEndpoint' = '<yourTunnelEndpoint>',
  'project' = '<yourProjectName>',
  'tablename' = '<yourTableName>',
  'accessid' = '<yourAccessId>',
  'accesskey' = '<yourAccessPassword>',
  'partition' = 'ds=20180905'
);

CREATE TEMPORARY TABLE blackhole_sink (
  cid varchar,
  invoke_count BIGINT
) with (
  'connector'='blackhole'
);

INSERT INTO blackhole_sink 
SELECT 
   cid,
   count(*) as invoke_count
FROM odps_source GROUP BY cid;
```

## 常见问题

-   [endPoint和tunnelEndpoint是指什么？如果配置错误会产生什么结果？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [启动作业时出现Akka超时报错，但是全量MaxCompute源表和增量MaxCompute获取Metadata速率正常，应该如何处理？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [全量MaxCompute和增量MaxCompute源表作业是否支持暂停作业后修改并发数，再恢复作业？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [全量MaxCompute和增量MaxCompute是如何读取MaxCompute数据的？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [引用MaxCompute作为数据源，在作业启动后，向已有的分区或者表里追加数据，这些新数据是否能被全量MaxCompute或增量MaxCompute源表读取？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [作业启动位点设置了2019-10-11 00:00:00， 为什么启动位点前的分区也会被全量MaxCompute源表读取？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [报错：ErrorMessage=Authorization Failed \[4019\], You have NO privilege'ODPS:\*\*\*'](t1916815.md#section_947_odv_kbe)

