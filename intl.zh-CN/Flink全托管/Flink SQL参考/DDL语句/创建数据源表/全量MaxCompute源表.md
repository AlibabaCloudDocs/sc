---
keyword: [源表, MaxCompute]
---

# 全量MaxCompute源表

本文为您介绍全量MaxCompute源表DDL定义、WITH参数、类型映射和常见问题。

**说明：** MaxCompute Connector可以作为Stream作业和Batch作业的源表使用。

## 什么是MaxCompute

大数据计算服务MaxCompute（原名ODPS）是一种快速、完全托管的EB级数据仓库解决方案，致力于批量结构化数据的存储和计算，提供海量数据仓库的解决方案及分析建模服务。MaxCompute详情请参见[什么是MaxCompute](/intl.zh-CN/产品简介/什么是MaxCompute.md)。

## 前提条件

已创建MaxCompute表，详情请参见[创建表](/intl.zh-CN/快速入门/通过MaxCompute客户端使用MaxCompute/创建和查看表.md)。

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
|endPoint|MaxCompute服务地址。|是|参见[各地域Endpoint对照表（外网连接方式）](/intl.zh-CN/准备工作/配置Endpoint.md)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址。|否|参见[各地域Endpoint对照表（外网连接方式）](/intl.zh-CN/准备工作/配置Endpoint.md)。**说明：** VPC环境下为必填。 |
|project|MaxCompute项目名称。|是|无|
|tableName|MaxCompute表名。|是|无|
|accessId|AccessKey ID。|是|无|
|accessKey|AccessKey Secret。|是|无|
|partition|分区名。|否|-   只存在一级分区的MaxCompute表

例如，如果只存在1个分区列`ds`，则``partition` = 'ds=20180905'`表示读`ds=20180905`分区的数据。

，值

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

-   Q：endPoint和tunnelEndpoint是指什么？如果配置错误会产生什么结果？

    A：endPoint和tunnelEndpoint参数说明参见[Endpoint](/intl.zh-CN/准备工作/配置Endpoint.md)。VPC环境中这两个参数如果配置错误可能会导致任务异常。

    -   endPoint配置错误：任务上线停滞在91%的进度。
    -   tunnelEndpoint配置错误：任务运行失败。
-   Q：全量MaxCompute数据存储是如何读取MaxCompute源表数据的？

    A：全量MaxCompute数据存储通过Tunnel读取全量MaxCompute数据，读取速度及带宽受全量MaxCompute Tunnel带宽限制。

-   Q：Fink作业启动后，MaxCompute源表是否能读取新追加到全量MaxCompute源表或者全量MaxCompute分区的数据？

    A：全量MaxCompute数据存储使用Tunnel读取表数据或者分区数据。Fink作业启动后，读取完成Reader就退出，不会读取新写入MaxCompute表和分区的数据。

-   Q：如何查看全量MaxCompute源表的分区名？

    A：您可以在**数据表详情**页面查看全量MaxCompute源表的分区名，步骤如下：

    1.  在[数据地图](https://meta.dw.alibaba-inc.com/store/index.html)，搜索表名称。
    2.  单击目标表名。
    3.  在数据表详情页面，右侧**明细信息** \> **分区信息** \> **分区名**查看MaxCompute分区名。
-   Q：引用MaxCompute作为数据源，在作业启动后，向已有的分区或者表里追加数据，这些新数据是否能被读取？

    A：数据不会被读取，而且可能导致作业失败。全量MaxCompute数据存储使用`ODPS DOWNLOAD SESSION`读取表数据或者分区数据。新建`DOWNLOAD SESSION`，服务端会创建一个Index文件，相当于创建`DOWNLOAD SESSION`一瞬间数据的映射，后续的数据读取以这个映射为基础。因此在新建 `DOWNLOAD SESSION` 后，追加到MaxCompute表或者分区里的数据，正常流程下是不会被读取的。分为两种情况：

    -   Tunnel在读取数据的过程中，写入新数据会产生报错`ErrorCode=TableModified,ErrorMessage=The specified table has been modified since the download initiated.`。
    -   Tunnel已经关闭，写入新数据。这些数据不会被读取的，因为Tunnel已经关闭。而且一旦作业发生Failover或者暂停恢复作业后，不能保证数据正确性，例如，已经读过的数据可能会重读，新追加的数据可能读不全。
-   Q：全量MaxCompute源表作业是否支持暂停、恢复和修改源表的并发度？

    A：系统根据并发度，计算每个并发读取指定分区的指定数据。此外，每个并发会把消费情况记录至State，以便暂停恢复后或者Failover后，系统可以从上次读取的位置继续读取数据。

    如果修改了增量MaxCompute源表的并发度后暂停恢复作业，会对作业产生无法预估的影响。因为已经读取的数据可能会被再次读取，没有读的数据可能会被遗漏。

-   Q：作业启动位点设置了`2019-10-11 00:00:00`， 为什么启动位点前的分区也会被读取？

    A：设置启动位点只对消息队列（例如DataHub）类型的数据源有效，对MaxCompute源表无效。Flink作业启动后数据读取的范围如下：

    -   分区表：读取当前所有分区。
    -   非分区表：读取当前存在的数据。

