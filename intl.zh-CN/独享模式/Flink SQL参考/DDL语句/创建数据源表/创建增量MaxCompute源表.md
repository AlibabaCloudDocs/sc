# 创建增量MaxCompute源表

本文为您介绍如何创建增量MaxCompute源表，以及创建维表时使用的WITH参数、类型映射和常见问题。

**说明：**

-   本文仅适用于Blink 3.5.0 hotfix版本。
-   增量MaxCompute源表不支持作为维表使用。
-   增量MaxCompute源表只支持MaxCompute分区表，不支持非分区表。

## 语法示例

```
create table odps_source(
    id int,
    user_name VARCHAR,
    content VARCHAR
) with (
    type = 'continuous-odps',
    endPoint = 'your_end_point_name',
    project = 'your_project_name',
    tableName = 'your_table_name',
    accessId = 'your_access_id',
    accessKey = 'your_access_key',
    startPartition = 'ds=20180905'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|connector类型|是|固定值为`continuous-odps`。|
|endPoint|MaxCompute服务本身的连接地址|是|详情请参见[配置Endpoint](/intl.zh-CN/准备工作/配置Endpoint.md)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址|-   是：VPC环境必填
-   否：其他非VPC环境

|详情请参见[配置Endpoint](/intl.zh-CN/准备工作/配置Endpoint.md)。|
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

|MaxCompute字段类型|实时计算Flink版字段类型|
|--------------|--------------|
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
CREATE TABLE odps_source (
    cid VARCHAR,
    rt DOUBLE,
) with (
    type = 'continuous-odps', 
    endPoint = 'your_end_point_name', 
    project = 'your_project_name',
    tableName = 'your_table_name',
    accessId = 'xxxx',
    accessKey = 'xxxx',
    startPartition = 'ds=20191201'
);

CREATE TABLE test (
    cid VARCHAR,
    rt DOUBLE,
) with (
    type='print'
);

INSERT INTO test 
SELECT 
    cid, rt FROM odps_source;
```

## 常见问题

-   Q：DDL中endPoint和tunnelEndpoint参数配置错误的影响？

    A：endPoint和tunnelEndpoint参数介绍请参见[开通MaxCompute服务的Region和服务连接对照表](/intl.zh-CN/准备工作/配置Endpoint.mdsection_f2d_51y_5db)。参数配置错误会导致以下问题：

    -   endPoint配置错误：任务上线进度停滞在91%处。
    -   tunnelEndpoint配置错误：任务运行失败。
-   Q：增量MaxCompute数据存储底层是如何读取MaxCompute表的？

    A：增量MaxCompute数据存储是通过tunnel读取MaxCompute数据，读取速度及带宽受MaxCompute tunnel带宽限制。

-   Q：实时计算Flink版作业启动后，增量MaxCompute源表是否能读取新写入到已经读取过的分区的数据？

    A：增量MaxCompute数据存储使用Tunnel读取MaxCompute源表分区的数据。实时计算Flink版作业启动后，读取完成Reader就退出，不会读取新写入MaxCompute源表已读取分区的数据。

-   Q：如何查看MaxCompute分区名？

    A：您可以在数据表详情中查看MaxCompute分区名，步骤如下：

    1.  在[数据地图](https://meta.dw.alibaba-inc.com/store/index.html)，搜索表名称。
    2.  在**所有表**区域，单击目标表名。
    3.  在数据表详情页面，右侧**明细信息** \> **分区信息** \> **分区名**查看MaxCompute分区名。
-   Q：引用增量MaxCompute作为数据源，在作业启动后，向已有的分区或者表里追加数据，这些新数据是否能被读取？

    A：数据不会被读取，而且可能导致作业失败。MaxCompute数据存储使用`ODPS DOWNLOAD SESSION`读取表数据或者分区数据。新建`DOWNLOAD SESSION`，服务端会创建一个Index文件，相当于创建`DOWNLOAD SESSION`那一瞬间数据的映射，后续的数据读取以这个映射为基础。因此在新建 `DOWNLOAD SESSION` 后，追加到MaxCompute表或者分区里的数据，正常流程下是不会被读取的。分为两种情况：

    -   Tunnel在读取数据的过程中，写入新数据会产生报错`ErrorCode=TableModified,ErrorMessage=The specified table has been modified since the download initiated.`。
    -   Tunnel已经关闭，写入新数据。这些数据不会被读取的，因为Tunnel已经关闭。而且一旦作业发生Failover或者暂停恢复作业后，不能保证数据正确性 （例如已经读过的数据可能会重读，新追加的数据可能读不全）。
-   Q：增量MaxCompute源表作业是否支持暂停、恢复和修改源表的并发度？

    增量MaxCompute源表作业暂不支持暂停、恢复和修改源表并发度。系统根据并发度，计算每个并发读取指定分区的指定数据。此外，每个并发会把消费情况记录至State，以便暂停恢复后或者Failover后，系统可以从上次读取的位置继续读取数据。

    如果修改了增量MaxCompute源表的并发度后暂停恢复作业，会对作业产生无法预估的影响。因为已经读取的数据可能会被再次读取，没有读的数据可能会被遗漏。

-   Q：监听到新分区的时候，如果该分区还有数据没有写完，如何处理？

    A：没有机制可以标志一个分区的数据是否已经完整。目前只要监听到新分区，就会读入。用增量MaxCompute源表读取一个MaxCompute分区表T，分区列是ds，表T的写入过程分为以下两种方式：

    -   不允许的写入方法：先创建好分区，例如ds=20191010，再往分区里写数据。增量MaxCompute源表监听到新分区ds=20191010，立刻读入，如果此时该分区还有数据没有写完，就会漏读数据。
    -   推荐的写入方法：不创建分区，先执行Insert overwrite table T partition \(ds='20191010'\) ...语句，作业结束且成功后，分区和数据一起可见。

