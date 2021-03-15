---
keyword: 创建交互式分析Hologres维表
---

# 创建交互式分析Hologres维表

本文为您介绍如何创建交互式分析Hologres维表，以及创建维表时使用的WITH参数、CACHE参数和类型映射。

**说明：**

-   本文仅适用于Blink 3.6.0及以上版本。如果您的Blink为3.6.0以下的版本，您可以[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)获取需要的JAR文件，安装使用。
-   建议您使用Hologres 0.7及以上版本。

## 什么是交互式分析Hologres

交互式分析Hologres是实时交互分析产品，兼容PostgreSQL协议，与大数据生态紧密连接，支持高并发、低延时实时分析与处理PB级数据，让您轻松使用现有BI（Business Intelligence）工具对数据进行多维分析和业务探索。

## 使用限制

-   创建Hologres维表时建议选择行存模式，列存模式对于点查场景性能开销较大。

    选择行存模式创建维表时必须设置主键，并且将主键设置为clustering key才可以工作。示例语句如下：

    ```
    begin;
    create table test(a int primary key, b text, c text, d float8, e int8);
    call set_table_property('test', 'orientation', 'row');
    call set_table_property('test', 'clustering_key', 'a');
    commit;
    ```

-   Hologres维表的主键必须是Blink Join On的字段，Blink Join On的字段也必须是维表完整的主键字段，两者必须完全匹配。
-   Hologres Blink Connector的维表功能不支持一对多的输出。

## 语法示例

实时计算Flink版支持使用Hologres作为维表，代码示例如下。

```
CREATE TABLE rds_dim_table(
  id INT,
  len INT,
  content VARCHAR,
  PRIMARY KEY (id),
  PERIOD FOR SYSTEM_TIME  --定义维表的变化周期。
) with (
  type='hologres',
  endpoint='...',
  dbname='...',
  tablename='...',
  username='...',
  password='...'
);
```

## WITH参数

|参数|描述|是否必选|备注|
|--|--|----|--|
|type|数据库类型|是|固定值为hologres。|
|endpoint|Hologres端点|是|无|
|tablename|表名称**说明：** 如果Schema不为Public时，则tableName需要填写为schema.tableName。

|是|无|
|dbname|数据库名称|是|无|
|username|用户名|是|无|
|password|密码|是|无|

## CACHE参数

|参数|描述|是否必选|示例值|
|--|--|----|---|
|cache|缓存策略|否|目前交互式分析Hologres维表支持以下三种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。

-   ALL：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查找数据都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新加载一遍全量Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）特别多的场景。

需要配置相关参数：缓存更新时间间隔（cacheTTLMs），更新时间黑名单（cacheReloadTimeBlackList）。


**说明：**

-   因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的两倍。
-   对于数据量比较大的维表，选择CACHE ALL时，可能会出现OOM或者Full GC耗时很久的情况，针对这个问题，可以选择以下两种解决方式：
    -   对于支持Cache All策略的维表，开启PartitionedJoin优化。3.6.0版本之前，每个并发默认加载维表全量数据；3.6.0版本之后，CACHE ALL策略支持PartitionedJoin优化。开启PartitionJoin优化后，每个并发只缓存自己并发所需要的数据。
    -   使用HBase或者RDS等Key-Value类型的维表。 |
|cacheSize|缓存大小|否|当缓存策略选择LRU时，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存失效时间，单位为毫秒|否|-   当缓存策略选择LRU时，可以设置缓存失效时间，默认不过期。
-   当缓存策略选择ALL时，缓存失效时间为缓存重新加载的间隔时间，默认不重新加载。 |
|partitionedJoin|根据分区查找数据|否|partitionedJoin参数的取值如下： -   false（默认值）
-   true |
|async|是否异步读取数据|否|async参数的取值如下： -   false（默认值）
-   true |

## 类型映射

|Hologres字段类型|实时计算Flink版字段类型|
|------------|--------------|
|INT|INT|
|INT\[\]|ARRAY<INT\>|
|BIGINT|BIGINT|
|BIGINT\[\]|ARRAY<BIGINT\>|
|REAL|FLOAT|
|REAL\[\]|ARRAY<FLOAT\>|
|DOUBLE PRECISION|DOUBLE|
|DOUBLE PRECISION\[\]|ARRAY<DOUBLE\>|
|BOOLEAN|BOOLEAN|
|BOOLEAN\[\]|ARRAY<BOOLEAN\>|
|TEXT|VARCHAR|
|TEXT\[\]|ARRAY<VARCHAR\>|
|NUMERIC|DECIMAL|
|DATE|DATE|
|TIMESTAMP WITH TIMEZONE|TIMESTAMP|

## 代码示例

实时计算Flink版包含Hologres维表的代码示例如下。

```
create table randomSource (a int, b VARCHAR, c VARCHAR) with (type = 'random');

create table test (
   a int, 
   b VARCHAR, 
   c VARCHAR, 
   PRIMARY KEY (a, b), PERIOD FOR SYSTEM_TIME
) with (
   type = 'hologres',
   ...
);

create table print_sink (
  a int, 
  b VARCHAR
) with (
  type = 'print', 
  `ignoreWrite` = 'false'
);

insert into print_sink
select randomSource.a, test.b from randomSource 
LEFT JOIN test FOR SYSTEM_TIME AS OF PROCTIME() 
on randomSource.a = test.a and randomSource.b = test.b;
```

