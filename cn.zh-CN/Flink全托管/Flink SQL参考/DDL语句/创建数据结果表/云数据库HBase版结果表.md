---
keyword: [HBase, 结果表]
---

# 云数据库HBase版结果表

本文为您介绍云数据库HBase版结果表DDL定义、WITH参数、转换关系、动态表和代码示例。

## 什么是云数据库HBase

[云数据库HBase]()是低成本、高扩展、云智能的大数据NoSQL，兼容标准HBase访问协议，提供低成本存储、高扩展吞吐、智能数据处理等核心优势，是为淘宝推荐、花呗风控、广告投放、监控大屏、菜鸟物流轨迹、支付宝账单、手淘消息等众多阿里巴巴核心服务提供支撑的数据库，具备PB规模、千万级并发、秒级伸缩、毫秒响应、跨机房高可用、全托管、全球分布等企业能力。

## 前提条件

-   已购买HBase集群并创建表，详情请参见[购买集群]()。
-   已设置白名单，详情请参见[设置白名单]()。

## 使用限制

仅Flink计算引擎VVR 2.0.0及以上版本支持云数据库HBase Connector。

## DDL定义

```
CREATE TABLE hbase_sink(
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
-   结果表中不需要将HBase表的所有列族和列都进行声明，只声明需要的即可。
-   除了类型为ROW的字段外，只能有一个原始类型（Atomic Type）的字段（例如STRING或BIGINT），该字段将被视作HBase的行键（Row Key），例如DDL定义中的rowkey。
-   必须将HBase的行键定义为结果表的主键（Primary Key），如果没有显示定义主键，默认使用行键作为主键。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型。|是|固定值为`cloudhbase`。|
|table-name|HBase表名。|是|无。|
|zookeeper.quorum|HBase的zookeeper地址。|是|无。|
|zookeeper.znode.parent|HBase在zookeeper中的根目录。|否|默认值为`/hbase`。 **说明：** 仅在HBase标准版中生效。 |
|userName|用户名。|否|**说明：** 仅在HBase增强版中生效。 |
|password|密码。|否|**说明：** 仅在HBase增强版中生效。 |
|haclient.cluster.id|HBase高可用实例ID。|否|只有访问同城主备实例时才需要配置。**说明：** 仅在HBase增强版中生效。 |
|retries.number|连接HBase客户端的重试次数。|否|默认值为31。|
|null-string-literal|HBase字段类型为字符串时，如果Flink字段数据为null，则将该字段赋值为null-string-literal，并写入Hbase。|否|默认值为null。|
|sink.buffer-flush.max-size|写入HBase前，内存中缓存的数据量（字节）大小。调大该值有利于提高HBase写入性能，但会增加写入延迟和内存使用。|否|默认值为2 MB，支持字节单位B、KB、MB和GB，不区分大小写。设置为0表示不进行缓存。|
|sink.buffer-flush.max-rows|写入HBase前，内存中缓存的数据条数。调大该值有利于提高HBase写入性能，但会增加写入延迟和内存使用。|否|默认值为1000，设置为0表示不进行缓存。|
|sink.buffer-flush.interval|将缓存数据周期性写入到HBase的间隔，可以控制写入HBase的延迟。|否|默认值为1秒，支持时间单位ms、s、min、h和d。设置为0表示关闭定期写入。|

## 转换关系

HBase数据通过org.apache.hadoop.hbase.util.Bytes转换成Flink的数据类型。解码过程有以下两种情况：

-   当数据为null时，对于非字符串类型的数据，会编码为空字节数组。
-   对于字符串类型的数据，取决于null-string-literal的值。

Flink与HBase的数据转换关系如下。

|Flink类型|HBase转换函数|
|-------|---------|
|CHAR|byte\[\] toBytes\(String s\)|
|VARCHAR|
|STRING|
|BOOLEAN|byte\[\] toBytes\(boolean b\)|
|BINARY|byte\[\]|
|VARBINARY|
|DECIMAL|byte\[\] toBytes\(BigDecimal v\)|
|TINYINT|new byte\[\] \{ val \}|
|SMALLINT|byte\[\] toBytes\(short val\)|
|INT|byte\[\] toBytes\(int val\)|
|BIGINT|byte\[\] toBytes\(long val\)|
|FLOAT|byte\[\] toBytes\(float val\)|
|DOUBLE|byte\[\] toBytes\(double val\)|
|DATE|将日期转换成自1970.01.01以来的天数，用int表示，并通过byte\[\] toBytes\(int val\) 转换成字节数组。|
|TIME|将时间转换成自00:00:00以来的毫秒数，用int表示，并通过byte\[\] toBytes\(int val\) 转换成字节数组。|
|TIMESTAMP|将时间戳转换成自1970-01-01 00:00:00以来的毫秒数，用long表示，并通过byte\[\] toBytes\(long val\) 转换成字节数组。|
|ARRAY|不支持。|
|MAP|不支持。|
|MULTISET|
|ROW|不支持。|

## 动态表

Flink部分结果数据需要按某列的值，作为动态列输入HBase。例如，某商品每小时的成交额作为动态列的数据，示例如下。

```
CREATE TEMPORARY TABLE datagen_source (
  id INT,
  f1hour STRING,
  f1deal BIGINT,
  f2day STRING,
  f2deal BIGINT
) with (
  'connector'='datagen'
);

CREATE TEMPORARY TABLE hbase_sink (
  rowkey INT,
  f1 ROW<`hour` STRING, deal BIGINT>,
  f2 ROW<`day` STRING, deal BIGINT>
) with (
  'connector'='cloudhbase',
  'table-name'='<yourTableName>',
  'zookeeper.quorum'='<yourZookeeperQuorum>',
  'dynamic.table'='true'
);

INSERT INTO hbase_sink
SELECT id, ROW(f1hour, f1deal), ROW(f2day, f2deal) FROM datagen_source;
```

**说明：**

-   当dynamic.table参数值为true时，表明该表为支持动态列的HBase表。
-   每个列族对应的ROW中必须声明两个字段：第1个字段的值表示动态列，第2个字段的值表示动态列的值。
-   如果src表存在一条\(1, "10", 100, "2020-7-26", 10000\)数据，代表ID为1的商品，在10:00-11:00点之间的成交额是100，在2020年7月26日当天的成交额是10000，则HBase中将插入行键为1的行，其中f1:10为100，f2:2020-7-26为10000。

## 代码示例

```
CREATE TEMPORARY TABLE datagen_source (
  rowkey INT,
  f1q1 INT,
  f2q1 STRING,
  f2q2 BIGINT,
  f3q1 DOUBLE,
  f3q2 BOOLEAN,
  f3q3 STRING
) with (
  'connector'='datagen'
);

CREATE TEMPORARY TABLE hbase_sink (
  rowkey INT,
  family1 ROW<q1 INT>,
  family2 ROW<q1 STRING, q2 BIGINT>,
  family3 ROW<q1 DOUBLE, q2 BOOLEAN, q3 STRING>,
  PRIMARY KEY (rowkey) NOT ENFORCE
) with (
  'connector'='cloudhbase',
  'table-name'='<yourTableName>',
  'zookeeper.quorum'='<yourZookeeperQuorum>'
);
 
INSERT INTO hbase_sink
SELECT rowkey, ROW(f1q1), ROW(f2q1, f2q2), ROW(f3q1, f3q2, f3q3) FROM datagen_source;
```

