---
keyword: [消息队列Kafka, 源表]
---

# 消息队列Kafka源表

本文为您介绍消息队列Kafka源表的DDL定义、WITH参数、元信息列和示例。

**说明：** 消息队列Kafka Connector仅支持读取kafka 0.10及以上版本的数据。

## 什么是Kafka源表

[消息队列Kafka版](/intl.zh-CN/产品简介/什么是消息队列Kafka版？.md)是阿里云提供的分布式、高吞吐、可扩展的消息队列服务。消息队列Kafka版广泛用于日志收集、监控数据聚合、流式数据处理、在线和离线分析等大数据领域。

## DDL定义

以下为创建Kafka源表的DDL示例，消息格式为CSV，包含5个字段。

```
CREATE TABLE kafkaTable (
    `user_id` BIGINT,
    `item_id` BIGINT,
    `category_id` BIGINT,
    `behavior` STRING,
    `topic` STRING METADATA VIRTUAL,
    `partition` BIGINT METADATA VIRTUAL
) WITH (
    'connector' = 'kafka',
    'topic' = 'my_excellent_topic',
    'properties.bootstrap.servers' = 'mykafka:9092',
    'properties.group.id' = 'my_excellent_group'
    'format' = 'csv',
    'scan.startup.mode' = 'earliest-offset'
)
```

在实际使用中请根据实际情况配置字段名和WITH参数。

## 元信息列

您可以在源表中定义元信息列，以获取Kafka消息的元信息。例如，当WITH参数中定义了多个topic时，如果在Kafka源表中定义了元信息列，那么Flink读取到的数据就会被标识是从哪个topic中读取的数据。

|Key|数据类型|说明|
|---|----|--|
|topic|STRING NOT NULL METADATA VIRTUAL|Kafka消息所在的Topic名称。|
|partition|INT NOT NULL METADATA VIRTUAL|Kafka消息所在的Partition ID。|
|headers|MAP<STRING, BYTES\> NOT NULL METADATA VIRTUAL|Kafka消息的消息头（header）。|
|leader-epoch|INT NOT NULL METADATA VIRTUAL|Kafka消息的Leader epoch。|
|offset|BIGINT NOT NULL METADATA VIRTUAL|Kafka消息的偏移量（offest）。|
|timestamp|TIMESTAMP\(3\) WITH LOCAL TIME ZONE NOT NULL METADATA VIRTUAL|Kafka消息的时间戳。|
|timestamp-type|STRING NOT NULL METADATA VIRTUAL|Kafka消息的时间戳类型： -   NoTimestampType：消息中没有定义时间戳。
-   CreateTime：消息产生的时间。
-   LogAppendTime：消息被添加到Kafka Broker的时间。 |

**说明：**

-   仅VVR 3.0.0及以后版本支持定义元信息列。
-   源表中定义的元信息列必须声明为METADATA VIRTUAL类型。

## WITH参数

|参数|说明|是否必选|数据类型|备注|
|--|--|----|----|--|
|connector|源表类型。|是|String|固定值为`kafka`。|
|topic|topic名称。|是|String|以分号 \(;\) 分隔多个topic名称，例如`topic-1;topic-2`。**说明：** topic和topic-pattern两个选项只能指定其中一个。 |
|topic-pattern|匹配读取topic名称的正则表达式。所有匹配该正则表达式的topic在作业运行时均会被订阅。|否|String|**说明：**

-   仅VVR 3.0.0及以后版本支持该参数。
-   topic和topic-pattern两个选项只能指定其中一个。 |
|properties.bootstrap.servers|Kafka Broker地址。|是|String|格式为`host:port,host:port,host:port`，以英文逗号（,）分割。|
|properties.group.id|Kafka消费组ID。|是|String|无。|
|properties.\*|Kafka配置。|否|String|后缀名必须匹配为Kafka官方文档中定义的配置。Flink会将properties.前缀移除，并将剩余的配置传递给Kafka客户端。例如可以通过`'properties.allow.auto.create.topics' = 'false'`来禁用自动创建topic。不建议通过以上方式修改'key.deserializer'和'value.deserializer'参数，因为它们会被kafka配置覆盖。 |
|format|Flink Kafka Connector在反序列化来自Kafka的消息体（value）时使用的格式。|是|String|取值如下：-   csv
-   json
-   avro
-   debezium-json
-   canal-json
-   maxwell-json
-   avro-confluent
-   raw

**说明：**

-   仅VVR 3.0.0及以后版本支持maxwell-json、avro-confluent和raw格式。
-   format和value.format只能配置其中一个，如果同时配置两个，则会有冲突。 |
|value.format|Flink Kafka Connector在反序列化来自Kafka的消息体（value）时使用的格式。|是|String|取值如下：-   csv
-   json
-   avro
-   debezium-json
-   canal-json
-   maxwell-json
-   avro-confluent
-   raw

**说明：**

-   仅VVR 3.0.0及以后版本支持maxwell-json、avro-confluent和raw格式。
-   format和value.format只能配置其中一个，如果同时配置两个，则会有冲突。 |
|key.format|反序列化Kafka消息键（key）时使用的格式。|否|String|取值如下：-   csv
-   json
-   avro
-   debezium-json
-   canal-json
-   maxwell-json
-   avro-confluent
-   raw

**说明：**

-   仅VVR 3.0.0及以后版本支持maxwell-json、avro-confluent和raw格式。
-   如果指定了key.format参数，则也必须指定key.fields参数。 |
|key.fields|Kafka消息键（key）解析出来的数据存放的字段。|否|String|多个字段名以分号（;）分隔。例如`field1;field2`。默认不配置该参数，因此key不会被解析，key数据将被丢弃。**说明：** 仅VVR 3.0.0及以后版本支持该参数。 |
|key.fields-prefix|为所有Kafka消息键（Key）指定自定义前缀，以避免与消息体（Value）格式字段重名。|否|String|默认情况下前缀为空。如果定义了前缀，表结构和配置项key.fields都需要使用带前缀的名称。当构建消息键字段时，前缀会被移除，将使用无前缀的名称。

**说明：**

-   仅VVR 3.0.0及以后版本支持该参数。
-   该配置项要求必须将value.fields-include配置为EXCEPT\_KEY。 |
|value.fields-include|在解析消息体时，是否要包含消息键字段。|否|String|取值如下：-   ALL（默认值）：所有定义的字段都存放消息体（Value）的解析出来的数据。
-   EXCEPT\_KEY：除去key.fields定义字段，剩余的定义字段可以用来存放消息体（Value）解析出来的数据。

**说明：** 仅VVR 3.0.0及以后版本支持该参数。 |
|scan.startup.mode|Kafka读取数据的启动位点。|否|String|取值如下：-   earliest-offset：从Kafka最早分区开始读取。
-   latest-offset：从Kafka最新位点开始读取。
-   group-offsets（默认值）：根据Group读取。
-   timestamp：从Kafka指定时间点读取。

需要在WITH参数中指定scan.startup.timestamp-millis参数。

-   specific\_offsets：从Kafka指定分区指定偏移量读取。

需要在WITH参数中指定scan.startup.specific-offsets参数。 |
|scan.startup.specific-offsets|在specific-offsets启动模式下，指定每个分区的启动偏移量。|否|String|例如：partition:0,offset:42;partition:1,offset:300|
|scan.startup.timestamp-millis|在timestamp启动模式下，指定启动位点时间戳。|否|Long|单位为毫秒。|

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
    'topic' = 'source',
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
    'topic' = 'sink',
    'properties.bootstrap.servers' = '<yourKafkaBrokers>',
    'properties.group.id' = '<yourKafkaConsumerGroupId>',
    'format' = 'csv'
);

INSERT INTO Kafka_sink SELECT id, name, age FROM Kafka_source;
```

## 常见问题

-   问题描述：Flink中的Commit Offset有什么作用？

    解决方案：Flink在每次Checkpoint成功时，才会向Kafka提交当前读取Offset。如果未开启Checkpoint，或者Checkpoint设置的间隔过大，在Kafka端可能会查询不到当前读取的Offset。

-   问题描述：为什么Flink和Kafka之间的网络是连通的，但是依然会有timeout expired while fetching topic metadata的报错？

    问题原因：Flink和Kafka之间的网络连通并不意味着能读取数据，只有Kafka Broker在bootstrap过程中返回的集群metadata中描述的Endpoint， 才可以连通Flink和Kafka，并读取到Kafka的数据，详情请参见[Flink-cannot-connect-to-Kafka](https://www.confluent.io/blog/kafka-client-cannot-connect-to-broker-on-aws-on-docker-etc/)。

    检查方法：

    1.  使用zkCli.sh或者zookeeper-shell.sh工具登录Kafka使用的Zookeeper。
    2.  执行`ls /brokers/ids`命令列出所有的Kafka Broker ID。
    3.  使用`get /brokers/ids/{your_broker_id}`命令查看Broker metadata信息。

        Endpoint信息显示在listener\_security\_protocol\_map中。

    4.  确认Flink是否可以连通该Endpoint。

        如果该Endpoint中使用了域名，请为Flink配置对应的域名解析服务。


