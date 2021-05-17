---
keyword: [消息队列Kafka, 源表]
---

# 消息队列Kafka源表

本文为您介绍消息队列Kafka源表的DDL定义、WITH参数和示例。

**说明：** 消息队列Kafka Connector仅支持读取kafka 0.10及以上版本的数据。

## 什么是Kafka源表

[消息队列Kafka版](/cn.zh-CN/产品简介/什么是消息队列Kafka版？.md)是阿里云提供的分布式、高吞吐、可扩展的消息队列服务。消息队列Kafka版广泛用于日志收集、监控数据聚合、流式数据处理、在线和离线分析等大数据领域。

## DDL定义

```
create table kafka_source(  
  user_id BIGINT,
  item_id BIGINT,
  category_id BIGINT,
  behavior STRING,
  ts TIMESTAMP(3)        
) with (
  'connector' = 'kafka',
  'topic' = '<yourTopicName>',
  'properties.bootstrap.servers' = '<yourKafkaBrokers>',
  'properties.group.id' = '<yourKafkaConsumerGroupId>',
  'format' = 'csv',
  'scan.startup.mode' = 'earliest-offset'
);
```

## WITH参数

|参数|说明|是否必选|数据类型|备注|
|--|--|----|----|--|
|connector|源表类型|是|STRING|固定值为`kafka`。|
|topic|源表对应的Topic|是|STRING|无|
|properties.bootstrap.servers|Kafka Broker地址|是|STRING|格式为`host:port,host:port,host:port`，以英文逗号（,）分割。|
|properties.group.id|Kafka消费组ID|是|STRING|无|
|format|Flink Kafka Connector在反序列化来自Kafka的消息时使用的格式。|是|STRING|格式取值如下：-   csv
-   json
-   avro
-   debezium-json
-   canal-json |
|scan.startup.mode|Kafka消费启动位点|否|STRING|启动位点取值如下：-   group-offsets（默认值）：根据Group读取。
-   earliest-offset：从Kafka最早分区开始读取。
-   latest-offset：从Kafka最新位点开始读取。
-   timestamp：从Kafka指定时间点读取。

需要在WITH参数中指定scan.startup.timestamp-millis参数。

-   specific\_offsets：从Kafka指定分区指定偏移量读取。

需要在WITH参数中指定scan.startup.specific-offsets参数。 |
|scan.startup.specific-offsets|在specific-offsets启动模式下，指定每个分区的启动偏移量。|否|STRING|例如：partition:0,offset:42;partition:1,offset:300|
|scan.startup.timestamp-millis|在timestamp启动模式下，指定启动位点时间戳。|否|LONG|单位为毫秒。|

如果您还需要直接配置Connector使用的Kafka Consumer，可以在Kafka Consumer配置参数前添加`properties`前缀，并将该Kafka Consumer配置信息追加至WITH参数。例如Kafka集群需要SASL（Simple Authentication and Security Layer）认证。

```
CREATE TABLE kafkaTable (
    ...
) WITH (
    ...
    'properties.security.protocol' = 'SASL_PLAINTEXT',
    'properties.sasl.mechanism' = 'PLAIN',
    'properties.sasl.jaas.config' = 'org.apache.kafka.common.security.plain.PlainLoginModule required username="USERNAME" password="PASSWORD";'
);
```

Kafka Consumer配置项详情请参见[Consumer Configs](https://kafka.apache.org/documentation/#consumerconfigs)。

## 从kafka中读取数据后插入Kafka示例

从名称为source的Topic中读取Kafka数据，再写入名称为sink的Topic，数据使用CSV格式。

```
CREATE TEMPORARY TABLE Kafka_source (
    id INT,
    name STRING,
    age INT
) WITH (
    'connector' = 'kafka',
    'topic' = '<yourTopicName>',
    'properties.bootstrap.servers' = '<yourKafkaBrokers>',
    'properties.group.id' = '<yourKafkaConsumerGroupId>',
    'format' = 'csv'
);

CREATE TEMPORARY TABLE Kafka_sink (
    id INT,
    name STRING,
    age INT
) WITH (
    'connector' = 'kafka',
    'topic' = '<yourTopicName>',
    'properties.bootstrap.servers' = '<yourKafkaBrokers>',
    'properties.group.id' = '<yourKafkaConsumerGroupId>',
    'format' = 'csv'
);

INSERT INTO Kafka_sink SELECT id, name, age FROM Kafka_source;
```

## 常见问题

Q：Flink中的COMMIT OFFSET有什么作用？

A：Flink默认采用commitOffsetOnCheckpointing，用户设置的Commit Offset策略不生效。只有开启checkpoint后，在每次checkpoint成功时，才会commit当前分区消费的offset至Kafka，以便在作业失败恢复过程中，从上一次checkpoint的commit位点开始消费，保证计算的Exactly Once。如果将checkpoint间隔设置过大，Kafka端可能会查询不到当前消费offset。

