---
keyword: [Upsert Kafka, 源表]
---

# Upsert Kafka源表

本文为您介绍Upsert Kafka源表的DDL定义、WITH参数和示例。

## 什么是Upsert Kafka

Upsert Kafka基于开源Flink社区的Upsert-Kafka版本实现，详情请参见[Upsert Kafka SQL 连接器](https://ci.apache.org/projects/flink/flink-docs-master/zh/dev/table/connectors/upsert-kafka.html)。Upsert Kafka可以产生Changelog流，支持以UPSERT方式从Kafka Topic中读取数据或将数据写入Kafka Topic。其中每条数据记录代表一个更新或删除事件，原理如下：

-   Kafka Topic中存在相应的Key，则以UPDATE操作将Key的值更新为数据记录中的Value。
-   Kafka Topic中不存在相应的Key，则以INSERT操作将Key的值写入Kafka Topic。
-   Key对应的Value为空，会被视作DELETE操作。

## 前提条件

已创建消息队列Kafka资源，详情请参见[步骤三：创建资源](/cn.zh-CN/快速入门/步骤三：创建资源.md)。

## 使用限制

仅Flink计算引擎VVR 3.0.1及以上版本支持Upsert Kafka Connector。

## DDL定义

以表名pageviews\_per\_region为例介绍DDL定义。

```
CREATE TABLE pageviews_per_region (
  user_region STRING,
  pv BIGINT,
  uv BIGINT,
  PRIMARY KEY (user_region) NOT ENFORCED
) WITH (
  'connector' = 'upsert-kafka',
  'topic' = '<yourTopicName>',
  'properties.bootstrap.servers' = '<yourServers>',
  'key.format' = 'avro',
  'value.format' = 'avro'
);
```

**说明：** Upsert Kafka必须声明主键。

## WITH参数

|参数|说明|是否必选|数据类型|备注|
|--|--|----|----|--|
|connector|源表类型|是|String|固定值为`upsert-kafka`。|
|topic|源表对应的Topic|是|String|无|
|properties.bootstrap.servers|Kafka Broker地址|是|String|格式为`host:port,host:port,host:port`，以英文逗号（,）分割。|
|key.format|Upsert Kafka消息中Key部分反序列化的格式|是|String|支持的格式如下：-   csv
-   json
-   avro |
|value.format|Upsert Kafka消息中Value部分反序列化的格式|是|String|支持的格式如下：-   csv
-   json
-   avro |
|value.fields-include|指定出现在Value中的字段|是|String|取值如下：-   ALL：默认值，Schema中所有字段，包括主键字段。
-   EXCEPT\_KEY：Schema中所有字段，不包括主键字段。 |
|key.fields-prefix|为主键的所有字段定义一个自定义前缀，避免Key与Value字段的名称冲突。|否|String|默认情况下，前缀为空。如果定义了前缀，则表的Schema和key.fields都将使用前缀名称。当构造Kafka Key对应格式的数据类型时，主键名中的前缀将被删除，并采用名字中非前缀部分作为最终的名字。

**说明：** key.fields-prefix参数生效，要求value.fields-include必须设置为EXCEPT\_KEY。 |
|properties.\*|指定Kafka参数|否|String|后缀名必须匹配定义在[Apache Kafka文档](https://kafka.apache.org/documentation/#configuration)中的参数名。Flink会自动移除properties.前缀，并将转换后的参数名及值传入Kafka客户端。例如，您可以通过设置`'properties.allow.auto.create.topics' = 'false'`来禁止自动创建Topic。**说明：** 参数`key.deserializer`和`value.deserializer`是不允许通过该方式指定参数的，因为Flink会重写这些参数的值。 |

## 示例

```
--创建upsert-kafka类型源表。
CREATE TABLE pageviews_per_region (
  user_region STRING,
  pv BIGINT,
  uv BIGINT,
  PRIMARY KEY (user_region) NOT ENFORCED
) WITH (
  'connector' = 'upsert-kafka',
  'topic' = '<yourTopicName>',
  'properties.bootstrap.servers' = '<yourServers>',
  'key.format' = 'avro',
  'value.format' = 'avro'
);

--创建Kafka类型结果表。
CREATE TABLE pageviews_per_region_db (
  user_region STRING,
  pv BIGINT,
  uv BIGINT,
  PRIMARY KEY (user_region) NOT ENFORCED
) WITH (
  'connector' = 'kafka',
  'topic' = '<yourTopicName>',
  'properties.bootstrap.servers' = '<yourKafkaBrokers>',
  'format' = 'csv'
);

--将upsert-kafka源表数据写入Kafka结果表。
INSERT INTO pageviews_per_region_db
SELECT user_region, pv, uv FROM pageviews_per_region;
```

