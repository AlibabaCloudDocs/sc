---
keyword: [云数据库HBase版, 维表]
---

# 云数据库HBase版维表

本文为您介绍云数据库HBase版维表的DDL定义、WITH参数、CACHE参数和转换关系。

## 什么是云数据库HBase

[云数据库HBase]()是低成本、高扩展、云智能的大数据NoSQL，兼容标准HBase访问协议，提供低成本存储、高扩展吞吐、智能数据处理等核心优势，是为淘宝推荐、花呗风控、广告投放、监控大屏、菜鸟物流轨迹、支付宝账单、手淘消息等众多阿里巴巴核心服务提供支撑的数据库，具备PB规模、千万级并发、秒级伸缩、毫秒响应、跨机房高可用、全托管、全球分布等企业能力。

## 前提条件

-   已购买HBase集群并创建表，详情请参见[购买集群]()。
-   已设置白名单，详情请参见[设置白名单]()。

## 使用限制

仅Flink计算引擎VVR 2.0.0及以上版本支持云数据库HBase Connector。

## DDL定义

```
CREATE TABLE hbase_dim(
  rowkey INT,
  family1 ROW<q1 INT>,
  family2 ROW<q2 STRING, q3 BIGINT>,
  family3 ROW<q4 DOUBLE, q5 BOOLEAN, q6 STRING>
) with (
  'connector'='cloudhbase',
  'table-name'='<yourTableName>',
  'zookeeper.quorum'='<yourZookeeperQuorum>'
);
```

-   HBase的列族（Column Family）必须声明为ROW类型，列族名即该ROW的字段名。例如，DDL定义中声明了family1、family2和family3三个列族。
-   HBase列族中的列（Cloumn）与对应ROW中嵌套的每个字段对应，列名即字段名。例如，DDL定义中列族family2声明了q2和q3两列。
-   除了类型为ROW的字段外，只能有一个原始类型（Atomic Type）的字段（例如STRING或BIGINT），该字段将被视作HBase的行键（Row Key），例如DDL定义中的rowkey。
-   必须将HBase的行键定义为结果表的主键（Primary Key），如果没有显示定义主键，默认使用行键作为主键。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|维表类型。|是|固定值为`cloudhbase`。|
|table-name|HBase表名。|是|无。|
|zookeeper.quorum|HBase的zookeeper地址。|是|无。|
|zookeeper.znode.parent|HBase在zookeeper中的根目录。|否|默认值为`/hbase`。 **说明：** 仅在HBase标准版中生效。 |
|userName|用户名。|否|**说明：** 仅在HBase增强版中生效。 |
|password|密码。|否|**说明：** 仅在HBase增强版中生效。 |
|haclient.cluster.id|HBase高可用实例ID。|否|只有访问同城主备实例时才需要配置。**说明：** 仅在HBase增强版中生效。 |
|retries.number|HBase客户端的重试次数。|否|默认值为31。|
|null-string-literal|字段类型为字符串时，如果HBase中的数据为该值的字节数组，则该字段的值为null。|否|默认值为Null。|

## CACHE参数

|参数|描述|是否必选|示例值|
|--|--|----|---|
|cache|缓存策略。|否|目前云数据库HBase版维表支持以下三种缓存策略： -   None：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。

-   ALL（默认值）：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查找数据都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新加载一遍全量Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）特别多的场景。

需要配置相关参数：缓存更新时间间隔（cacheTTLMs），更新时间黑名单（cacheReloadTimeBlackList）。


**说明：** 因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的两倍。 |
|cacheSize|缓存大小。|否|当缓存策略选择LRU时，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存失效时间，单位为毫秒。|否|-   当缓存策略选择LRU时，可以设置缓存失效时间，默认不过期。
-   当缓存策略选择ALL时，缓存失效时间为缓存重新加载的间隔时间，默认不重新加载。 |
|cacheEmpty|是否缓存空结果|否|默认值为true。|
|cacheReloadTimeBlackList|更新时间黑名单。在缓存策略选择为ALL时，启用更新时间黑名单，防止在此时间内做Cache更新（例如双11场景）。|否|默认为空，格式为`2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11 08:00`。分隔符的使用情况如下所示： -   用逗号`,`来分隔多个黑名单。
-   用箭头`->`来分割黑名单的起始结束时间。 |
|cacheScanLimit|缓存策略选择ALL时启用。读取全量HBase数据，RPC（Remote Procedure Call Protocol）服务端一次返回给客户端的行数。|否|默认值为100条。|

## 转换关系

HBase数据通过org.apache.hadoop.hbase.util.Bytes转换成Flink的数据类型。解码过程有以下两种情况：

-   对于Flink的非字符串类型，如果HBase中的值为空字节数组，则解码为null。
-   对于Flink的字符串类型，如果HBase中的值为null-string-literal字节数组，则解码为null。

Flink与HBase的数据转换关系如下。

|Flink类型|HBase转换函数|
|-------|---------|
|CHAR|String toString\(byte\[\] b\)|
|VARCHAR|
|STRING|
|BOOLEAN|boolean toBoolean\(byte\[\] b\)|
|BINARY|byte\[\]|
|VARBINARY|
|DECIMAL|BigDecimal toBigDecimal\(byte\[\] b\)|
|TINYINT|bytes\[0\]|
|SMALLINT|short toShort\(byte\[\] bytes\)|
|INT|int toInt\(byte\[\] bytes\)|
|BIGINT|long toLong\(byte\[\] bytes\)|
|FLOAT|float toFloat\(byte\[\] bytes\)|
|DOUBLE|double toDouble\(byte\[\] bytes\)|
|DATE|HBase字节数组通过int toInt\(byte\[\] bytes\) 转换成int，表示自1970.01.01以来的天数。|
|TIME|HBase字节数组通过int toInt\(byte\[\] bytes\) 转换成int，表示自00:00:00以来的毫秒数。|
|TIMESTAMP|HBase字节数组通过long toLong\(byte\[\] bytes\) 转换成long，表示自1970-01-01 00:00:00以来的毫秒数。|
|ARRAY|不支持|
|MAP / MULTISET|不支持|
|ROW|不支持|

## 代码示例

包含HBase维表的实时计算作业代码示例如下。

```
CREATE TABLE datagen_source (
  a INT,
  b BIGINT,
  c STRING,
  `proc_time` AS PROCTIME()
) with (
  'connector'='datagen'
);

CREATE TABLE hbase_dim (
  rowkey INT,
  family1 ROW<col1 INT>,
  family2 ROW<col1 STRING, col2 BIGINT>,
  family3 ROW<col1 DOUBLE, col2 BOOLEAN, col3 STRING>
) WITH (
  'connector' = 'cloudhbase',
  'table-name' = '<yourTableName>',
  'zookeeper.quorum' = '<yourZookeeperQuorum>'
);

CREATE TABLE blackhole_sink(
  a INT,
  f1c1 INT,
  f3c3 STRING
) with (
  'connector' = 'blackhole' 
);
  
INSERT INTO blackhole_sink
     SELECT a, family1.col1 as f1c1,  family3.col3 as f3c3 FROM datagen_source
JOIN hbase_dim FOR SYSTEM_TIME AS OF src.`proc_time` as h ON src.a = h.rowkey;
```

