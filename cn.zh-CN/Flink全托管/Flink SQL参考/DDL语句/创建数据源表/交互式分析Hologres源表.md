---
keyword: [创建交互式分析Hologres源表, Hologres源表]
---

# 交互式分析Hologres源表

本文为您介绍交互式分析Hologres源表DDL定义、WITH参数、代码示例和类型映射。

**说明：**

-   Hologres Connector不支持访问Hologres外部表。Hologres外部表详情请参见[外部表](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/外部表.md)。
-   Hologres Connector可以作为Stream作业和Batch作业的源表使用。

## 什么是交互式分析Hologres

[交互式分析Hologres](/cn.zh-CN/产品简介/什么是Hologres.md)兼容PostgreSQL协议，与大数据生态紧密连接，支持高并发、低延时实时分析处理PB级数据，让您轻松使用现有BI（Business Intelligence）工具对数据进行多维分析和业务探索。

## DDL定义

```
create table hologres_source(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourAccessID>',
  'password'='<yourAccessSecret>',
  'endpoint'='<yourEndpoint>',
  'field_delimiter'='|' --该参数可选。
);
```

**说明：**

-   Flink不支持在源表中定义计算列。
-   Flink默认以批模式读取Hologres源表数据，即只扫描一次Hologres全表，扫描结束，消费结束，新到Hologres源表的数据不会被读取。从VVP 2.4.0版本开始，支持实时消费Hologres数据。如何实时消费Hologres数据，请参见[Flink实时消费Hologres](#section_lih_mxx_vik)。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|源表类型|是|固定值为hologres。|
|dbname|数据库名称|是|无。|
|tablename|表名称 **说明：** 如果Schema不为Public时，需要在表名称前面添加模式名称的前缀，即schema.tableName。

|是|无。|
|username|用户名，请填写阿里云账号的AccessKey ID。|是|无。|
|password|密码，请填写阿里云账号的AccessKey Secret。|是|无。|
|endpoint|Hologres服务地址。|是|详情请参见[访问域名](/cn.zh-CN/实例管理/访问域名.md)。|
|field\_delimiter|导出数据时，不同行之间使用的分隔符。 **说明：** 只有在bulkread = true时有效。

|否|默认值为"\\u0002"。|
|binlog|是否消费Binlog数据。|否|参数取值如下： -   true：消费Binlog数据。
-   false（默认值）：不消费Binlog数据。

 **说明：** 仅VVP 2.4.0及以上版本支持该参数。 |
|binlogMaxRetryTimes|读取Binlog数据出错后的重试次数。|否|默认值为60。 **说明：** 仅VVP 2.4.0及以上版本支持该参数。 |
|binlogRetryIntervalMs|读取Binlog数据出错后的重试时间间隔。|否|默认值为2000，单位为毫秒。 **说明：** 仅VVP 2.4.0及以上版本支持该参数。 |
|binlogBatchReadSize|批量读取Binlog的数据行数。|否|默认值为16。 **说明：** 仅VVP 2.4.0及以上版本支持该参数。 |
|cdcMode|是否采用CDC模式读取Binlog数据。|否|参数取值如下： -   true：CDC模式读取Binlog数据。
-   false（默认值）：非CDC模式读取Binlog数据。

 **说明：** 仅VVP 2.4.0及以上版本支持该参数。 |

## 代码示例

```
CREATE TEMPORARY TABLE hologres_source (
  name varchar, 
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourAccessID>',
  'password'='<yourAccessSecret>',
  'endpoint'='<yourEndpoint>',
  'field_delimiter'='|' --该参数可选。
);

CREATE TEMPORARY TABLE blackhole_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT 
) with (
  'connector'='blackhole'
);

INSERT INTO blackhole_sink
SELECT 
   name, age, birthday
from hologres_source;
```

## 类型映射

交互式分析Hologres和Flink字段类型对应关系如下，建议使用该对应关系进行DDL声明。

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
|TIMESTAMP WITH TIMEZONE（别名TIMESTAMPTZ）|TIMESTAMP|

## Flink实时消费Hologres

VVP 2.4.0及以上版本，Hologres Connector支持实时消费Hologres。实时消费Hologres，即实时消费Hologres的Binlog数据。以下为您介绍Flink实时消费Hologres的详情：

-   使用限制
    -   已存在的表无法修改表属性开启Binlog，需要重新建表。
    -   不支持开启分区表父表的Binlog，请使用非分区表。
    -   暂不支持实时消费TIMESTAMP类型的数据，因此创建Hologres表时，请使用TIMESTAMPTZ类型。
    -   开启Binlog后，理论上列存表的开销要大于行存表的开销。因此，对于数据更新频繁的场景，建议使用存储格式为行存的表开启Binlog。
-   开启Binlog

    实时消费功能默认关闭，因此在Hologres控制台上创建表的DDL时，需要设置binlog.level和binlog.ttl参数，示例如下。

    ```
    begin;
    create table test_message_src(
     id int primary key, 
     title text not null, 
     body text
    );
    call set_table_property('test_message_src', 'orientation', 'row');
    call set_table_property('test_message_src', 'clustering_key', 'id');
    call set_table_property('test_message_src', 'binlog.level', 'replica'); 
    call set_table_property('test_message_src', 'binlog.ttl', '86400'); 
    commit;
    ```

    其中，binlog.level设置为replica即代表开启Binlog，binlog.ttl为Binlog的TTL，单位为秒。

-   注意事项
    -   UPDATE操作会产生两条Binlog记录，分别为更新操作前和操作后的数据记录，因此您会消费到两条数据。但是，Hologres Binlog功能会保证这两条记录是连续的且更新前的Binlog记录在前，更新后的Binlog记录在后。
    -   建议Flink作业并发数和Hologres Table的Shard个数保持一致。

        您可以在Hologres控制台上，使用以下语句查看Table的Shard数，其中tablename为您的业务表名称。

        ```
        select tg.property_value from hologres.hg_table_properties tb join hologres.hg_table_group_properties tg on tb.property_value = tg.tablegroup_name where tb.property_key = 'table_group' and tg.property_key = 'shard_count' and table_name = '<tablename>';
        ```

-   消费模式
    -   非CDC模式

        该模式下Source消费的Binlog数据是作为普通的Flink数据传递给下游节点的，即所有数据都是INSERT类型的数据，您可以根据业务情况选择如何处理特定hg\_binlog\_event\_type类型的数据。源表DDL代码示例如下。

        ```
        create table test_message_src_binlog_table(
          hg_binlog_lsn BIGINT,
          hg_binlog_event_type BIGINT,
          hg_binlog_timestamp_us BIGINT,
          id INTEGER,
          title VARCHAR,
          body VARCHAR
        ) with (
          'connector'='hologres',
          'dbname'='<yourDbname>',
          'tablename'='<yourTablename>',
          'username'='<yourAccessID>',
          'password'='<yourAccessSecret>',
          'endpoint'='<yourEndpoint>',
          'binlog' = 'true',
          'binlogMaxRetryTimes' = '10',
          'binlogRetryIntervalMs' = '500',
          'binlogBatchReadSize' = '100'
        );
        ```

    -   CDC模式

        该模式下Source消费的Binlog数据，将根据hg\_binlog\_event\_type自动为每行数据设置准确的Flink RowKind类型，例如，INSERT、DELETE、UPDATE\_BEFORE和UPDATE\_AFTER类型，这样就能完成表的数据的镜像同步，类似MySQL或Postgres的CDC功能。源表DDL代码示例如下。

        ```
        create table test_message_src_binlog_table(
          id INTEGER,
          title VARCHAR,
          body VARCHAR
        ) with (
          'connector'='hologres',
          'dbname'='<yourDbname>',
          'tablename'='<yourTablename>',
          'username'='<yourAccessID>',
          'password'='<yourAccessSecret>',
          'endpoint'='<yourEndpoint>',
          'binlog' = 'true',
          'cdcMode' = 'true',
          'binlogMaxRetryTimes' = '10',
          'binlogRetryIntervalMs' = '500',
          'binlogBatchReadSize' = '100'
        );
        ```

-   实现原理

    一条Binlog的字段由Binlog系统字段和用户Table字段组成，字段定义如下：

    |字段名|字段类型|说明|
    |---|----|--|
    |hg\_binlog\_lsn|BIGINT|Binlog系统字段，表示Binlog序号，Shard内部单调递增不保证连续，不同Shard之间不保证唯一和有序。|
    |hg\_binlog\_event\_type|BIGINT|Binlog系统字段，表示当前记录所表示的修改类型，参数取值如下：     -   INSERT=5：表示当前Binlog为插入一条新的记录。
    -   DELETE=2：表示当前Binlog为删除一条已有的记录。
    -   BEFORE\_UPDATE=3：表示当前Binlog为更新操作前的记录。
    -   AFTER\_UPDATE=7：表示当前Binlog为更新操作后的记录。 |
    |hg\_binlog\_timestamp\_us|BIGINT|Binlog系统字段，系统时间戳，单位为μs。|
    |user\_table\_column\_1|用户定义|用户的表字段。|
    |...|...|用户的表字段。|
    |user\_table\_column\_n|用户定义|用户的表字段。|


