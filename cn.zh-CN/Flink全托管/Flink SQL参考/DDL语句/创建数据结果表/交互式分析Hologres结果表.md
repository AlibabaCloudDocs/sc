---
keyword: 创建交互式分析Hologres结果表
---

# 交互式分析Hologres结果表

本文为您介绍交互式分析Hologres结果表DDL定义、WITH参数、流式语义和类型映射。

**说明：**

-   Hologres Connector不支持将结果数据写入Hologres外部表。Hologres外部表详情请参见[外部表](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/外部表.md)。
-   Hologres Connector可以作为Stream作业和Batch作业的源表使用。

## 什么是交互式分析Hologres

[交互式分析Hologres](/cn.zh-CN/产品简介/什么是Hologres.md)兼容PostgreSQL协议，与大数据生态紧密连接，支持高并发、低延时实时分析处理PB级数据，让您轻松使用现有BI（Business Intelligence）工具对数据进行多维分析和业务探索。

## DDL定义

```
create table hologres_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourUsername>',
  'password'='<yourPassword>',
  'endpoint'='<yourEndpoint>',
  'field_delimiter'='|' --该参数可选。
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为hologres。|
|dbname|数据库名称|是|无|
|tablename|表名称**说明：** 如果Schema不为Public时，则tableName需要填写为schema.tableName。

|是|无|
|username|用户名|是|无|
|password|密码|是|无|
|endpoint|Hologres端点|是|格式为<ip\>:<port\>。|
|field\_delimiter|Hologres Sink支持将一个STRING字段按照field\_delimiter切分成数组导入Hologres。|否|默认值为"\\u0002"。|
|mutatetype|数据写入模式，详情请参见[流式语义](#section_yce_507_nhr)。|否|默认值为insertorignore。|
|partitionrouter|是否写入分区表。|否|默认值为false。|
|ignoredelete|是否忽略撤回消息。|否|默认值为false。**说明：** 仅在使用流式语义时生效。 |
|createparttable|当写入分区表时，是否根据分区值自动创建不存在的分区表。**说明：** 如果分区值中存在短划线（-），暂不支持自动创建分区表。

|否|-   false（默认值）：不会自动创建。
-   true：自动创建。

**说明：**

-   VVR 2.1以上版本支持该参数。
-   请确保分区值不会出现脏数据，否则会创建错误的分区表导致Failover，建议慎用该参数。 |
|useRpcMode|自VVP 2.4.0版本开始，默认通过JDBC实现Hologres Connector。VVP 2.4.0以前版本默认是通过RPC实现Hologres Connector。|否|通过JDBC实现会占用SQL连接，导致JDBC链接数增加。如果您需要降低SQL连接数，则可以设置该参数值为true。**说明：** 仅VVP 2.4.0及以上版本支持该参数。 |
|connectionSize|单个Flink结果表任务所创建的JDBC连接池大小。如果作业性能不足，建议您增加连接池大小。连接池大小和数据吞吐成正比。

|否|默认值为3，单位为个。**说明：** 仅VVP 2.4.0及以上版本支持该参数。 |
|jdbcWriteBatchSize|JDBC模式，Hologres Sink节点数据攒批的最大值。|否|默认值为256，单位为数据行数。**说明：** 仅VVP 2.4.0及以上版本支持该参数。 |
|jdbcWriteFlushInterval|JDBC模式，Hologres Sink节点数据攒批写入Hologres的最长等待时间。|否|默认值为10000，即10秒。**说明：** 仅VVP 2.4.0及以上版本支持该参数。 |

## 流式语义

流处理，也称为流数据或流事件处理，即对一系列无界数据或事件连续处理。执行流数据或流事件处理的系统通常允许您指定一种可靠性模式或处理语义，保证整个系统处理数据的准确性，因为网络或设备故障等可能会导致数据丢失。

根据Hologres Sink的配置和Hologres表的属性，流式语义分为以下两种：

-   Exactly-once（仅一次）：即使在发生各种故障的情况下，系统只处理一次数据或事件。
-   At-least-once（至少一次）：如果在系统完全处理之前丢失了数据或事件，则从源头重新传输，因此可以多次处理数据或事件。如果第一次重试成功，则不必进行后续重试。

在Hologres结果表中使用流式语义，您需要注意以下几点：

-   如果Hologres物理表未设置主键，则Hologres Sink使用At-least-once语义。
-   如果Hologres物理表已设置主键，则Hologres Sink通过主键确保Exactly-once语义。当同主键数据出现多次时，您需要设置mutatetype参数确定更新结果表的方式，mutatetype取值如下：

    -   insertorignore（默认值）：保留首次出现的数据，忽略后续所有数据。
    -   insertorreplace：整行替换已有数据。
    -   insertorupdate：替换部分已有数据。例如一张表有a、b、c和d四个字段，a是PK（Primary Key），写入Hologres时只写入a和b两个字段，在PK重复的情况下，系统只会更新b字段，c和d保持不变。
    **说明：**

    -   当mutatetype设置为insertorupdate或insertorreplace时，系统根据主键更新数据。
    -   Flink定义的结果表中的数据列数不一定要和Hologres物理表的列数一致，您需要保证缺失的列没有非空约束，即列值可以为Null，否则会报错。
-   默认情况下，Hologres Sink只能向一张表导入数据。如果导入数据至分区表的父表，即使导入成功，也会查询数据失败。您可以设置参数partitionRouter为true，开启自动将数据路由到对应分区表的功能。注意事项如下：
    -   tablename参数需要填写为父表的表名。
    -   Connector不会自动创建分区表，因此，需要您提前手动创建需要导入数据的分区表，否则会导入失败。

## 宽表Merge和局部更新功能

在把多个流的数据写到一张Hologres宽表的场景中，会涉及到宽表Merge和数据的局部更新。下面通过一个示例来介绍如何设置。

假设有两个Flink数据流，一个数据流中包含A、B和C字段，另一个数据流中包含A、D和E字段，Hologres宽表WIDE\_TABLE包含A、B、C、D和E字段，其中A字段为主键。具体操作如下：

1.  使用Flink SQL创建两张Hologres结果表，其中一张表只声明A、B和C字段，另一张表只声明A、D和E字段。这两张表都映射至宽表WIDE\_TABLE。
2.  两张结果表的属性设置：
    -   mutatetype设置为insertorupdate，可以根据主键更新数据。
    -   ignoredelete设置为true，防止回撤消息产生Delete请求。
3.  将两个Flink数据流的数据分别INSERT至对应的结果表中。

**说明：** 在上述场景中，有如下限制：

-   宽表必须有主键。
-   每个数据流的数据都必须包含完整的主键字段。
-   列存模式的宽表Merge场景在高RPS的情况下，CPU使用率会偏高，建议关闭表中字段的Dictionary encoding功能。

## 类型映射

|Hologres字段类型|Flink字段类型|
|------------|---------|
|INT|INT|
|INT\[\]|ARRAY|
|BIGINT|BIGINT|
|BIGINT\[\]|ARRAY|
|REAL|FLOAT|
|REAL\[\]|ARRAY|
|DOUBLE PRECISION|DOUBLE|
|DOUBLE PRECISION\[\]|ARRAY|
|BOOLEAN|BOOLEAN|
|BOOLEAN\[\]|ARRAY|
|TEXT|VARCHAR|
|TEXT\[\]|ARRAY|
|NUMERIC|DECIMAL|
|DATE|DATE|
|TIMESTAMP WITH TIMEZONE|TIMESTAMP|

