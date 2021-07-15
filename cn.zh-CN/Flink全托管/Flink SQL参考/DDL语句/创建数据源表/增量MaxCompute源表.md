---
keyword: [增量MaxCompute, 源表]
---

# 增量MaxCompute源表

本文为您介绍增量MaxCompute源表DDL定义、WITH参数、类型映射和常见问题。

## 什么是MaxCompute

大数据计算服务MaxCompute（原名ODPS）是一种快速、完全托管的EB级数据仓库解决方案，致力于批量结构化数据的存储和计算，提供海量数据仓库的解决方案及分析建模服务。MaxCompute详情请参见[什么是MaxCompute](/cn.zh-CN/产品简介/什么是MaxCompute.md)。

## 前提条件

已创建MaxCompute表，详情请参见[创建表](/cn.zh-CN/快速入门/通过MaxCompute客户端使用MaxCompute/创建表.md)。

## 使用限制

仅Flink计算引擎VVR 2.1.2及以上版本支持增量MaxCompute Connector。

## DDL定义

```
create table odps_source(
  id INT,
  user_name VARCHAR,
  content VARCHAR
) with (
  'connector' = 'continuous-odps', 
  'endpoint' = '<yourEndpoint>',
  'tunnelEndpoint' = '<yourTunnelEndpoint>',
  'project' = '<yourProjectName>',
  'tablename' = '<yourTableName>',
  'accessid' = '<yourAccessKeyId>',
  'accesskey' = '<yourAccessKeySecret>',
  'startpartition' = 'ds=2018****'
);
```

**说明：**

-   WITH参数需要全部小写。
-   增量MaxCompute源表不支持作为维表使用。
-   增量MaxCompute源表只支持MaxCompute分区表，不支持非分区表。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|源表类型。|是|固定值为`continuous-odps`。|
|endPoint|MaxCompute服务本身的连接地址。|是|详情请参见[配置Endpoint](/cn.zh-CN/准备工作/Endpoint.md)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址。|是|详情请参见[配置Endpoint](/cn.zh-CN/准备工作/Endpoint.md)。|
|project|表所属的project名称。|是|无。|
|tableName|表名。|是|无。|
|accessId|AccessKey ID。|是|无。|
|accessKey|AccessKey Secret。|是|无。|
|startPartition|指定读取的起始分区。系统加载分区列表时，会把每个分区列表的所有分区和startPartition按照字母顺序进行比较，加载满足条件的分区的数据。 此外，增量MaxCompute源表可以持续监听增量MaxCompute分区表。读完已有的分区后，任务不会退出，且持续监听并读入新分区。

**说明：**

-   增量MaxCompute源表中必须存在一级分区，二级分区可选。
-   如果指定二级分区，必须写在一级分区的后面。
-   如果startPartition指定的分区不存在，系统会从下一个分区开始读取数据。

|是|例如，指定startPartition是ds=20191201，表示加载增量MaxCompute表里所有满足ds \>= 20191201的分区数据。如果一个增量MaxCompute分区表，有一级分区ds和二级分区type两个分区列，假设表里有以下5个分区：

-   ds=20191201,type=a
-   ds=20191201,type=b
-   ds=20191202,type=a
-   ds=20191202,type=b
-   ds=20191202,type=c

不同startPartition，满足分区的列表如下：

-   ds=20191202
    -   ds=20191202,type=a
    -   ds=20191202,type=b
    -   ds=20191202,type=c
-   ds=20191201,type=c
    -   ds=20191202,type=a
    -   ds=20191202,type=b
    -   ds=20191202,type=c |

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
|DECIMAL|DECIMAL|
|BINARY|VARBINARY|
|STRING|VARCHAR|

**说明：**

-   增量MaxCompute源表暂不支持CHAR、VARCHAR、ARRAY、MAP和STRUCT数据类型。
-   您可以临时使用STRING替换VARCHAR。

## 代码示例

增量MaxCompute源表每天产生一个分区，分区列是ds，从ds=20191201分区开始，加载后续的所有分区，并一直监听新分区的产生。

```
--读增量MaxCompute表，读取的分区范围是[ds=20191201，∞)。
CREATE TEMPORARY TABLE odps_source (
  cid VARCHAR,
  rt DOUBLE
) with (
  'connector' = 'continuous-odps', 
  'endpoint' = '<yourEndpoint>',
  'tunnelEndpoint' = '<yourTunnelEndpoint>',
  'project' = '<yourProjectName>',
  'tablename' = '<yourTableName>',
  'accessid' = '<yourAccessKeyId>',
  'accesskey' = '<yourAccessKeySecret>',
  'startpartition' = 'ds=2018****'
);

CREATE TEMPORARY TABLE blackhole_sink (
  cid VARCHAR,
  rt DOUBLE
) WITH (
  'connector'='blackhole'
);

INSERT INTO blackhole_sink 
SELECT 
    cid, rt FROM odps_source;
```

## 常见问题

-   [endPoint和tunnelEndpoint是指什么？如果配置错误会产生什么结果？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [启动作业时出现Akka超时报错，但是全量MaxCompute源表和增量MaxCompute获取Metadata速率正常，应该如何处理？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [全量MaxCompute和增量MaxCompute源表作业是否支持暂停作业后修改并发数，再恢复作业？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [全量MaxCompute和增量MaxCompute是如何读取MaxCompute数据的？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [引用MaxCompute作为数据源，在作业启动后，向已有的分区或者表里追加数据，这些新数据是否能被全量MaxCompute或增量MaxCompute源表读取？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [作业启动位点设置了2019-10-11 00:00:00， 为什么启动位点前的分区也会被全量MaxCompute源表读取？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [增量MaxCompute源表监听到新分区时，如果该分区还有数据没有写完，如何处理？](/cn.zh-CN/Flink全托管/常见问题.md)

