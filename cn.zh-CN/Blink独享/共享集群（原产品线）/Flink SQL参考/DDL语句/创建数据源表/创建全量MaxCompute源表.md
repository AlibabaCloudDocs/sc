---
keyword: [源表, MaxCompute]
---

# 创建全量MaxCompute源表

本文为您介绍如何创建全量MaxCompute源表，以及创建源表时使用的WITH参数和类型映射。

**说明：**

-   本文仅适用于Blink 2.2.7及以上版本。
-   与数据总线DataHub、Kafka等数据源不同，全量MaxCompute源表通常作为有限流表使用。Blink 3.4.4版本临时支持将全量MaxCompute源表作为无限流表使用，实现不间断监听并读取新增分区的功能。Blink 3.5.0版本废弃该功能，如果您需要使用无限流MaxCompute源表，请参见[创建增量MaxCompute源表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据源表/创建增量MaxCompute源表.md)。

## DDL定义

实时计算Flink版支持使用全量MaxCompute作为源表输入，示例代码如下。

```
create table odps_source(
    id INT,
    user_name VARCHAR,
    content VARCHAR
) with (
    type = 'odps',
    endPoint = 'http://service.cn.maxcompute.aliyun-inc.com/api',
    project = '<projectName>',
    tableName = '<tableName>',
    accessId = '<yourAccessKeyId>',
    accessKey = '<yourAccessKeySecret>',
    `partition` = 'ds=2018****' --如果您的MaxCompute源表为非分区表，不声明该参数即可。
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|endPoint|MaxCompute服务地址|是|参见[开通MaxCompute服务的Region和服务连接对照表](/cn.zh-CN/准备工作/配置Endpoint.md)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址|否|参见[开通MaxCompute服务的Region和服务连接对照表](/cn.zh-CN/准备工作/配置Endpoint.md)。**说明：** VPC环境下为必填。 |
|project|MaxCompute项目名称|是|无|
|tableName|MaxCompute表名|是|无|
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
|partition|分区名|否|-   只存在一个分区的全量MaxCompute表

例如，如果只存在1个分区列`ds`，则``partition` = 'ds=20180905'`表示读`ds=20180905`分区的数据。

-   存在多个分区的全量MaxCompute表

例如，如果存在2个分区列`ds`和`hh`，则``partition`='ds=20180905,hh=*'`表示读`ds=20180905`分区的数据。

**说明：** 分区过滤时需要声明所有分区的值。例如，上述示例中，只声明``partition` = 'ds=20180905'`，则不会读取任何分区。 |
|subscribeNewPartition|是否监听符合条件的新分区|否|默认值为false，不监听新产生的分区。**说明：**

-   subscribeNewPartition参数值为`true`时，不可以指定partition的参数值 ，否则会造成无法读取新产生的分区的状况。
-   该参数只在Blink 3.4.4版本临时存在，Blink 3.5.0版本废弃该参数，请使用[创建增量MaxCompute源表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据源表/创建增量MaxCompute源表.md)。 |
|subscribeIntervalInSec|监听新分区的间隔|否|默认值为30，单位为秒。**说明：** 监听间隔设置太小，会对全量MaxCompute MetaData服务造成压力，有可能导致监听服务失败。 |
|maxPartitionCount|未设置Partition参数时，读取当前分区标的分区个数。|否|默认值为100。**说明：** 仅Blink 3.0及以上版本支持该参数。 |

## 类型映射

|全量MaxCompute字段类型|实时计算Flink版字段类型|
|----------------|--------------|
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

包含全量MaxCompute源表的实时计算Flink版作业代码示例如下。

```
CREATE TABLE odps_source (
    cid varchar,
    rt DOUBLE,
) with (
    type = 'odps', 
    endPoint = '<yourEndpointName>',
    project = '<yourProjectName>',
    tableName = '<yourTableName>',
    accessId = '<yourAccessId>',
    accessKey = '<yourAccessPassword>',
    partition = 'ds=20190712'
);

CREATE TABLE test (
    cid varchar,
    invoke_count BIGINT
) with (
  type='print'
);

INSERT INTO test 
SELECT 
    cid,
    count(*) as invoke_count
FROM odps_source GROUP BY cid;
```

## 常见问题

-   Q：DDL中endPoint和tunnelEndpoint参数配置错误，有什么影响？

    A：endPoint和tunnelEndpoint参数介绍请参见[开通MaxCompute服务的Region和服务连接对照表](/cn.zh-CN/准备工作/配置Endpoint.md)。参数配置错误会导致以下问题：

    -   endPoint配置错误：任务上线进度停滞在91%处。
    -   tunnelEndpoint配置错误：任务运行失败。
-   Q：全量MaxCompute数据存储底层是如何读取全量MaxCompute源表的？

    A：全量MaxCompute数据存储通过Tunnel读取全量MaxCompute数据的，读取速度及带宽受全量MaxCompute Tunnel带宽限制。

-   Q：实时计算Flink版作业启动后，全量MaxCompute源表是否能读取新写入到全量MaxCompute表或者全量MaxCompute分区的数据？

    A：全量MaxCompute数据存储使用Tunnel读取表数据或者分区数据。实时计算Flink版作业启动后，读取完成Reader后，不再读取新写入全量MaxCompute表和分区的数据。

-   Q：实时计算Flink版作业启动后，全量MaxCompute源表如何能够读取到全量MaxCompute表或者全量MaxCompute分区的数据？

    A：实时计算Flink版 3.4及以上版本支持subscribeNewPartition参数，控制是否需要监听新产生的分区。新产生的数据可以写入到新的分区，示例代码如下。

    ```
    CREATE TABLE blink_source (
        cid varchar,
        rt DOUBLE,
    ) with (
        type = 'odps', 
        endPoint = '<yourEndpointName>',
        project = '<yourProjectName>',
        tableName = '<yourTableName>',_table_name',
        subscribeNewPartition = 'true'
        --请注意：如果需要监听新的分区，则不能指定partition值。
        accessId = '<yourAccessKeyId>',
        accessKey = '<yourAccessKeySecret>',
    );
    
    CREATE TABLE test (
        cid varchar,
        invoke_count BIGINT
    ) with (
      type='print'
    );
    
    INSERT INTO test 
    SELECT 
        cid,
        count(*) as invoke_count
    FROM blink_source GROUP BY cid;
    ```

    **说明：** 全量MaxCompute表新分区产生的数据需要写入实时计算Flink版全量MaxCompute源表的新分区。如果写入现有分区，则不生效。

-   Q：如何查看全量MaxCompute源表的分区名？

    A：您可以在数据表详情页面查看全量MaxCompute源表的分区名，步骤如下：

    1.  在[数据地图](https://meta.dw.alibaba-inc.com/store/index.html)，搜索表名称。
    2.  单击目标表名。
    3.  在数据表详情页面，右侧**明细信息** \> **分区信息** \> **分区名**查看全量MaxCompute分区名。
-   Q：WITH参数中partition值是否可以使用`max_pt()`或者`max_pt_with_done()`？

    A：不推荐使用。如果需要使用，请务必了解在没有开启监听新分区和开启监听新分区的场景下`max_pt()`在全量MaxCompute源表的作用：

    -   未开启监听新分区

        任务启动后，会通过全量MaxCompute MetaData服务获取当前全量MaxCompute 源表所有分区列表，从中读取`max_pt()`。读取结束后，Reader退出。不再读取该分区中的新数据或者监听新分区。

    -   开启监听新分区

        任务启动后，会通过全量MaxCompute MetaData服务获取当前全量MaxCompute源表所有分区列表，从中读取`max_pt()`。读取结束后，不再读取该分区中的新数据。间隔一定周期（参见subscribeIntervalInSec参数）监听是否有新分区产生。如果有新分区产生，读取新产生的分区列表，从中获取`max_pt()`，并读取。读取完成后还会等待下次监听事件。如果没有新分区产生，等待下次监听事件。

-   Q：提交任务阶段出现Akka超时报错，但是全量MaxCompute源表获取Metadata速率正常，应该如何处理？

    A：请合并小文件，具体步骤请参见文档[MaxCompute什么情况下会产生小文件？如何解决小文件问题？](/cn.zh-CN/常见问题/优化诊断.md)

-   Q：引用全量MaxCompute作为数据源，在作业启动后，向已有的分区或者表里追加数据，这些新数据是否能被读取？

    A：数据不会被读取，而且可能导致作业失败。全量MaxCompute数据存储使用`ODPS DOWNLOAD SESSION`读取表数据或者分区数据。新建`DOWNLOAD SESSION`，服务端会创建一个Index文件，相当于创建`DOWNLOAD SESSION`那一瞬间数据的映射，后续的数据读取以这个映射为基础。因此在新建 `DOWNLOAD SESSION` 后，追加到全量MaxCompute表或者分区里的数据，正常流程下是不会被读取的。分为两种情况：

    -   Tunnel在读取数据的过程中，写入新数据会产生报错`ErrorCode=TableModified,ErrorMessage=The specified table has been modified since the download initiated.`。
    -   Tunnel已经关闭，写入新数据。这些数据不会被读取的，因为Tunnel已经关闭。而且一旦作业发生Failover或者暂停恢复作业后，不能保证数据正确性，例如，已经读过的数据可能会重读，新追加的数据可能读不全。
-   Q：全量MaxCompute源表作业是否支持暂停、恢复和修改源表的并发度？

    A：全量MaxCompute源表作业不支持暂停、恢复和修改源表并发度。系统根据并发度，计算每个并发应该读哪些分区的哪一段内容，并且每个并发会把自己消费的情况记录到State里。以便暂停恢复后或者Failover后，可以从上次读取的位置继续读取。这个逻辑是建立在并发度确定的前提下的。如果修改了全量MaxCompute源表的并发度后进行暂停恢复操作，对作业产生的影响是无法预估的，因为已经读取的数据可能会再次读取，没有读的数据反而被遗漏。

-   Q：作业启动位点设置了`2019-10-11 00:00:00`， 为什么启动位点前的分区也会被读取？

    A：设置启动位点只对消息队列类型的数据源有效 （例如DataHub），对全量MaxCompute源表无效。实时计算Flink版作业启动后数据读取的范围如下：

    -   分区表：读取当前所有分区。
    -   非分区表：读取当前存在的数据。

