---
keyword: [消息队列Kafka, 结果表]
---

# 消息队列Kafka结果表

本文为您介绍消息队列Kafka结果表的DDL定义、元信息列、WITH参数和示例。

## 什么是消息队列Kafka

消息队列Kafka版是阿里云提供的分布式、高吞吐、可扩展的消息队列服务。消息队列Kafka版广泛用于日志收集、监控数据聚合、流式数据处理、在线和离线分析等大数据领域。

## DDL定义

以下为创建Kafka结果表的DDL示例，消息格式为CSV，包含5个字段。

```
CREATE TABLE kafkaTable (
    user_id BIGINT,
    item_id BIGINT,
    category_id BIGINT,
    behavior STRING,
    ts TIMESTAMP(3) METADATA FROM ‘timestamp’
) WITH (
    'connector' = 'kafka',
    'topic' = 'my_excellent_topic',
    'properties.bootstrap.servers' = 'mykafka:9092',
    'format' = 'csv',
)
```

在实际使用中请根据实际情况配置字段名和WITH参数。

## 元信息列

您可以在结果表中定义元信息列，以写入Kafka消息的元信息。

|Key|数据类型|说明|
|---|----|--|
|headers|MAP<STRING, BYTES\> NOT NULL METADATA VIRTUAL|Kafka消息的消息头（header）。|
|timestamp|TIMESTAMP\(3\) WITH LOCAL TIME ZONE NOT NULL METADATA VIRTUAL|Kafka消息的时间戳。|

**说明：**

-   仅VVR 3.0.0及以后版本支持定义元信息列。
-   源表中定义的元信息列必须声明为METADATA VIRTUAL类型。

## WITH参数

|参数|说明|是否必选|数据类型|备注|
|--|--|----|----|--|
|connector|结果表类型。|是|String|固定值为`kafka`。|
|topic|Topic名称。|是|String|**说明：** 结果表不支持写入多个topic。 |
|properties.bootstrap.servers|Kafka Broker访问地址。|是|String|格式为`host:port,host:port,host:port`，以英文逗号（,）分割。|
|properties.\*|Kafka配置。|否|String|后缀名必须匹配为Kafka官方文档中定义的配置。Flink会将properties.前缀移除，并将剩余的配置传递给Kafka客户端。例如可以通过`'properties.allow.auto.create.topics' = 'false'`来禁用自动创建topic。不建议通过以上方式修改'key.deserializer'和'value.deserializer'参数，因为它们会被Kafka Connector的配置覆盖。 |
|format|Flink Kafka Connector在反序列化来自Kafka的消息时使用的格式。format与value.forma的作用相同。|是|String|取值如下：-   csv
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
|value.format|Flink Kafka Connector在反序列化来自Kafka的消息体（value）时使用的格式。value.format与format的作用相同。|是|String|取值如下：-   csv
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
|sink.partitioner|从Flink分区到Kafka分区的映射模式。|否|String|映射模式取值如下：-   fixed：每个Flink分区对应至多一个Kafka分区。
-   round-robin：Flink分区中的数据将被轮流分配至Kafka的各个分区。
-   自定义FlinkKafkaPartitioner的子类：如果fixed和round-robin不满足您的需求，您可以自定义映射模式到FlinkKafkaPartitioner的子类。例如`org.mycompany.MyPartitioner`。 |

如果您还需要直接配置Connector使用的Kafka Producer，可以在Kafka Producer配置参数前添加properties前缀，并将该Kafka Producer配置信息追加至WITH参数。例如Kafka集群需要SASL（Simple Authentication and Security Layer）认证。

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

Kafka Producer配置项详情请参见[Producer Configs](https://kafka.apache.org/documentation/#producerconfigs)。

## 从Kafka中读取数据后插入Kafka示例

从名称为source的Topic中读取Kafka数据，再写入名称为sink的Topic，数据使用CSV格式。

```
CREATE TEMPORARY TABLE kafka_source (
    id INT,
    name STRING,
    age INT
) WITH (
    'connector' = 'kafka',
    'topic' = 'source',
    'properties.bootstrap.servers' = '<yourKafkaBrokers>',
    'properties.group.id' = '<yourPropertiesGroupid>',
    'format' = 'csv'
);

CREATE TEMPORARY TABLE kafka_sink (
    id INT,
    name STRING,
    age INT
) WITH (
    'connector' = 'kafka',
    'topic' = 'sink',
    'properties.bootstrap.servers' = '<yourKafkaBrokers>',
    'format' = 'csv'
);

INSERT INTO kafka_sink SELECT id, name, age FROM kafka_source;
```

