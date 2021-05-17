---
keyword: [Upsert Kafka, 结果表]
---

# Upsert Kafka结果表

本文为您介绍Upsert Kafka结果表的DDL定义、WITH参数和示例。

**说明：** Upsert Kafka Connector仅支持将结果数据写入到kafka 0.10及以上版本。

## 什么是Upsert Kafka

Upsert Kafka基于开源Flink社区的Upsert Kafka版实现，详情请参见[Upsert Kafka SQL连接器](https://ci.apache.org/projects/flink/flink-docs-master/zh/dev/table/connectors/upsert-kafka.html)。Upsert Kafka可以消费ChangeLog流，支持Flink将INSERT和UPDATE\_AFTER数据作为正常的Kafka消息写入Kafka Topic，并将DELETE数据以Value为空的Kafka消息写入Kafka Topic，即以Upsert方式将数据写入Kafka Topic。此外，Flink将根据主键列的值对数据进行分区，从而保证主键上的消息有序，因此同一主键上的更新或删除消息将落在同一分区中。

## DDL定义

以表名upsert\_kafka\_sink为例介绍DDL定义。

```
CREATE TABLE upsert_kafka_sink (
  user_region STRING,
  pv BIGINT,
  uv BIGINT,
  PRIMARY KEY (user_region) NOT ENFORCED
) WITH (
  'connector' = 'upsert-kafka',
  'topic' = '<yourTopicName>',
  'properties.bootstrap.servers' = '...',
  'key.format' = 'avro',
  'value.format' = 'avro'
);
```

**说明：** Upsert Kafka必须声明主键。

## WITH参数

|参数|说明|是否必选|数据类型|备注|
|--|--|----|----|--|
|connector|结果表类型|是|STRING|固定值为`upsert-kafka`。|
|topic|结果表对应的Topic|是|STRING|无|
|properties.bootstrap.servers|Kafka Broker地址|是|STRING|格式为`host:port,host:port,host:port`，以英文逗号（,）分割。|
|key.format|Upsert Kafka消息中Key部分序列化的格式|是|STRING|支持的格式如下：-   csv
-   json
-   avro |
|value.format|Upsert Kafka消息中Value部分序列化的格式|是|STRING|支持的格式如下：-   csv
-   json
-   avro |
|value.fields-include|指定出现在Value中的字段|否|String|取值如下：-   ALL（默认值）：Schema中所有字段，包括主键字段。
-   EXCEPT\_KEY：Schema中所有字段，不包括主键字段。 |
|properties.\*|指定Kafka参数|否|String|后缀名必须匹配定义在[Apache Kafka文档](https://kafka.apache.org/documentation/#configuration)中的参数名。Flink会自动移除properties.前缀，并将转换后的参数名及值传入Kafka客户端。例如，您可以通过设置`'properties.allow.auto.create.topics' = 'false'`来禁止自动创建Topic。**说明：** 已在WITH参数定义的Kafka参数是不允许通过该方式指定参数，因为Flink会重写该参数的值。例如`'key.deserializer'`和`'value.deserializer'`。 |

## 示例

```
--创建统计PV和UV的源表。
CREATE TABLE pageviews (
  user_id BIGINT,
  page_id BIGINT,
  viewtime TIMESTAMP,
  user_region STRING,
  WATERMARK FOR viewtime AS viewtime - INTERVAL '2' SECOND
) WITH (
  'connector' = 'kafka',
  'topic' = '<yourTopicName>',
  'properties.bootstrap.servers' = '...',
  'format' = 'json'
);


--创建Upsert Kafka结果表。
CREATE TABLE pageviews_per_region (
  user_region STRING,
  pv BIGINT,
  uv BIGINT,
  PRIMARY KEY (user_region) NOT ENFORCED
) WITH (
  'connector' = 'upsert-kafka',
  'topic' = '<yourTopicName>',
  'properties.bootstrap.servers' = '...',
  'key.format' = 'avro',
  'value.format' = 'avro'
);

--将统计的PV、UV数据写入结果表中。
INSERT INTO pageviews_per_region 
SELECT
  user_region,
  COUNT(*),
  COUNT(DISTINCT user_id)
FROM pageviews
GROUP BY user_region;
```

