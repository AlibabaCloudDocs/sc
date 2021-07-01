---
keyword: [增量MaxCompute, 源表]
---

# 增量MaxCompute源表

本文为您介绍增量MaxCompute源表DDL定义、WITH参数、类型映射和常见问题。

## 什么是MaxCompute

大数据计算服务MaxCompute（原名ODPS）是一种快速、完全托管的EB级数据仓库解决方案，致力于批量结构化数据的存储和计算，提供海量数据仓库的解决方案及分析建模服务。MaxCompute详情请参见[什么是MaxCompute](/intl.zh-CN/产品简介/什么是MaxCompute.md)。

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
|connector|源表类型|是|固定值为`continuous-odps`。|
|endPoint|MaxCompute服务本身的连接地址|是|详情请参见[配置Endpoint](/intl.zh-CN/准备工作/配置Endpoint.md)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址|是|详情请参见[配置Endpoint](/intl.zh-CN/准备工作/配置Endpoint.md)。|
|project|表所属的project名称|是|无|
|tableName|表名|是|无|
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
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

-   Q：endPoint和tunnelEndpoint是指什么？如果配置错误会产生什么结果？

    A：endPoint和tunnelEndpoint参数说明参见[配置Endpoint](/intl.zh-CN/准备工作/配置Endpoint.md)。VPC环境中这两个参数如果配置错误可能会导致任务异常。

    -   endPoint配置错误：任务上线停滞在91%的进度。
    -   tunnelEndpoint配置错误：任务运行失败。
-   Q：增量MaxCompute数据存储是如何读取增量MaxCompute源表数据的？

    A：增量MaxCompute数据存储是通过Tunnel读取增量MaxCompute数据，读取速度及带宽受MaxCompute tunnel带宽限制。

-   Q：Flink作业启动后，增量MaxCompute源表是否能读取新追加到MaxCompute表或者MaxCompute分区的数据？

    A：增量MaxCompute数据存储使用Tunnel读取表数据或者分区数据。Flink作业启动后，读取完成Reader就退出，不会读取新写入MaxCompute表和分区的数据。

-   Q：如何查看MaxCompute分区名？

    A：您可以在数据表详情中查看MaxCompute分区名，步骤如下：

    1.  在[数据地图](https://meta.dw.alibaba-inc.com/store/index.html)，搜索表名称。
    2.  单击目标表名。
    3.  在数据表详情页面，右侧**明细信息** \> **分区信息** \> **分区名**查看MaxCompute分区名。
-   Q：引用增量MaxCompute作为数据源，在作业启动后，向已有的分区或者表里追加数据，这些新数据是否能被读取？

    A：数据不会被读取，而且可能导致作业失败。MaxCompute数据存储使用`ODPS DOWNLOAD SESSION`读取表数据或者分区数据。新建`DOWNLOAD SESSION`，服务端会创建一个Index文件，相当于创建`DOWNLOAD SESSION`那一瞬间数据的映射，后续的数据读取以这个映射为基础。因此在新建 `DOWNLOAD SESSION` 后，追加到MaxCompute表或者分区里的数据，正常流程下是不会被读取的。分为两种情况：

    -   Tunnel在读取数据的过程中，写入新数据会产生报错`ErrorCode=TableModified,ErrorMessage=The specified table has been modified since the download initiated.`。
    -   Tunnel已经关闭，写入新数据。这些数据不会被读取的，因为Tunnel已经关闭。而且一旦作业发生Failover或者暂停恢复作业后，不能保证数据正确性 （例如已经读过的数据可能会重读，新追加的数据可能读不全）。
-   Q：增量MaxCompute源表作业是否支持暂停、恢复和修改源表的并发度？

    增量MaxCompute源表作业暂不支持暂停、恢复和修改源表并发度。系统根据并发度，计算每个并发读取指定分区的指定数据。此外，每个并发会把消费情况记录至State，以便暂停恢复后或者Failover后，系统可以从上次读取的位置继续读取数据。

    如果修改了增量MaxCompute源表的并发度后暂停恢复作业，会对作业产生无法预估的影响。因为已经读取的数据可能会被再次读取，没有读的数据可能会被遗漏。

-   Q：监听到新分区时，如果该分区还有数据没有写完，如何处理？

    A：没有机制可以标志一个分区的数据是否已经完整。目前只要监听到新分区，就会读入。用增量MaxCompute源表读取一个MaxCompute分区表T，分区列是ds，表T的写入过程分为以下两种方式：

    -   不允许的写入方法：先创建好分区，例如ds=20191010，再往分区里写数据。增量MaxCompute源表监听到新分区ds=20191010，立刻读入，如果此时该分区还有数据没有写完，就会漏读数据。
    -   推荐的写入方法：不创建分区，先执行Insert overwrite table T partition \(ds='20191010'\) ...语句，作业结束且成功后，分区和数据一起可见。

