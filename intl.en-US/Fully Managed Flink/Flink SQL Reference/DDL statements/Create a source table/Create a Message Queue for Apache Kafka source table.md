---
keyword: [Message Queue for Apache Kafka, source table]
---

# Create a Message Queue for Apache Kafka source table

This topic describes how to create a Message Queue for Apache Kafka source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, metadata columns, and sample code used when you create a Message Queue for Apache Kafka source table.

**Note:** The Message Queue for Apache Kafka connector can read only data of Kafka 0.10 or later.

## Introduction to Message Queue for Apache Kafka source tables

[Message Queue for Apache Kafka](/intl.en-US/Introduction/What is Message Queue for Apache Kafka?.md) is a distributed, high-throughput, and scalable message queue service provided by Alibaba Cloud. This service is widely used in big data applications, such as log collection, monitoring data aggregation, streaming data processing, and both online and offline data analysis.

## DDL syntax

The following sample code shows how to use DDL statements to create a Message Queue for Apache Kafka source table. In this example, the message format is CSV and the table contains five fields.

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

You can configure the field names and parameters in the WITH clause based on your business requirements.

## Metadata columns

You can define metadata columns in the Message Queue for Apache Kafka source table to obtain the metadata of Message Queue for Apache Kafka messages. For example, if multiple topics are defined in the WITH clause and a metadata column is defined in the Message Queue for Apache Kafka source table, the topic from which data read by Flink is marked.

|Key|Data type|Description|
|---|---------|-----------|
|topic|STRING NOT NULL METADATA VIRTUAL|The name of the topic to which the Message Queue for Apache Kafka message belongs.|
|partition|INT NOT NULL METADATA VIRTUAL|The ID of the partition to which the Message Queue for Apache Kafka message belongs.|
|headers|MAP<STRING, BYTES\> NOT NULL METADATA VIRTUAL|Headers of the Message Queue for Apache Kafka message.|
|leader-epoch|INT NOT NULL METADATA VIRTUAL|The leader epoch of the Message Queue for Apache Kafka message.|
|offset|BIGINT NOT NULL METADATA VIRTUAL|The offset of the Message Queue for Apache Kafka message.|
|timestamp|TIMESTAMP\(3\) WITH LOCAL TIME ZONE NOT NULL METADATA VIRTUAL|The timestamp of the Message Queue for Apache Kafka message.|
|timestamp-type|STRING NOT NULL METADATA VIRTUAL|The timestamp type of the Message Queue for Apache Kafka message. Valid values: -   NoTimestampType: indicates that no timestamp is defined in the message.
-   CreateTime: indicates the time when the message was generated.
-   LogAppendTime: indicates the time when the message was added to Kafka brokers. |

**Note:**

-   Metadata columns can be defined only in Ververica Runtime \(VVR\) 3.0.0 and later.
-   The metadata columns defined in the source table must be declared as the METADATA VIRTUAL type.

## Parameters in the WITH clause

|Parameter|Description|Required|Data type|Remarks|
|---------|-----------|--------|---------|-------|
|connector|The type of the source table.|Yes|String|Set the value to `kafka`.|
|topic|The name of the topic.|Yes|String|Separate multiple topic names with semicolons \(;\), such as `topic- 1;topic-2`. **Note:** The topic parameter cannot be used with the topic-pattern parameter. |
|topic-pattern|The regular expression used to match topics. All topics with names that match the specified regular expression will be subscribed by the consumer when the job starts running.|No|String|**Note:**

-   Only VVR 3.0.0 and later versions support this parameter.
-   The topic parameter cannot be used with the topic-pattern parameter. |
|properties.bootstrap.servers|The IP addresses or endpoints of Kafka brokers in the Message Queue for Apache Kafka cluster.|Yes|String|Format: `host:port,host:port,host:port`. The IP addresses or endpoints are separated by commas \(,\).|
|properties.group.id|The ID of a Kafka consumer group.|Yes|String|None.|
|properties.\*|The Message Queue for Apache Kafka configurations.|No|String|The suffix must match the configuration defined in the Kafka Configuration documentation. Flink will remove the properties. prefixand pass the transformed key and values to the Kafka client. For example, you can set `properties.allow.auto.create.topics` to false to disable automatic topic creation. The values of the 'key.deserializer' and 'value.deserializer' parameters will be overridden by the Kafka configurations. Therefore, we recommend that you do not modify the settings of the two parameters by adding the properties. prefix. |
|format|The format that the Message Queue for Apache Kafka connector uses to deserialize the value field in a Message Queue for Apache Kafka message.|Yes|String|Valid values:-   csv
-   json
-   avro
-   debezium-json
-   canal-json
-   maxwell-json
-   avro-confluent
-   raw

**Note:**

-   Only VVR 3.0.0 and later versions support maxwell-json, avro-confluent, and raw formats.
-   The format parameter cannot be used with the value.format parameter. If you use the two parameters at the same time, a conflict occurs. |
|value.format|The format that the Message Queue for Apache Kafka connector uses to deserialize the value field in a Message Queue for Apache Kafka message.|Yes|String|Valid values:-   csv
-   json
-   avro
-   debezium-json
-   canal-json
-   maxwell-json
-   avro-confluent
-   raw

**Note:**

-   Only VVR 3.0.0 and later versions support maxwell-json, avro-confluent, and raw formats.
-   The format parameter cannot be used with the value.format parameter. If you use the two parameters at the same time, a conflict occurs. |
|key.format|The format that the Message Queue for Apache Kafka connector uses to deserialize the key field in a Message Queue for Apache Kafka message.|No|String|Valid values:-   csv
-   json
-   avro
-   debezium-json
-   canal-json
-   maxwell-json
-   avro-confluent
-   raw

**Note:**

-   Only VVR 3.0.0 and later versions support maxwell-json, avro-confluent, and raw formats.
-   If the key.format parameter is specified, you must also specify the key.fields parameter. |
|key.fields|The fields parsed from the key field in a Message Queue for Apache Kafka message.|No|String|Multiple field names are separated by semicolons \(;\), such as, `field 1;field2`. This parameter is not configured by default. Therefore, the key field is not parsed and the key data is discarded. **Note:** Only VVR 3.0.0 and later versions support this parameter. |
|key.fields-prefix|A custom prefix for all keys in Message Queue for Apache Kafka messages. You can specify this parameter to prevent name conflicts with the value fields.|No|String|The prefix is empty by default. If a prefix is defined, the prefix must be added to the name of the table schema and the name of the fields specified in the key.fields parameter. When a key field is constructed, the prefix is removed and the name without a prefix is used.

**Note:**

-   Only VVR 3.0.0 and later versions support this parameter.
-   If you specify this parameter, you must set value.fields-include to EXCEPT\_KEY. |
|value.fields-include|Specifies whether to include the key field when the value field is parsed.|No|String|Valid values:-   ALL: All defined fields can store the data that is parsed from the value field. This is the default value.
-   EXCEPT\_KEY: All defined fields except those specified by the key.fields parameter can store the data that is parsed from the value field.

**Note:** Only VVR 3.0.0 and later versions support this parameter. |
|scan.startup.mode|The start offset for Message Queue for Apache Kafka to read data.|No|String|Valid values:-   earliest-offset: Message Queue for Apache Kafka reads data from the earliest partition.
-   latest-offset: Message Queue for Apache Kafka reads data from the latest offset.
-   group-offsets: Message Queue for Apache Kafka reads data by group. This is the default value.
-   timestamp: Message Queue for Apache Kafka reads data from a specified time.

You must specify the scan.startup.timestamp-millis parameter in the WITH clause.

-   specific-offsets: reads data from a specified partition of Message Queue for Apache Kafka at a specified offset.

You must specify the scan.startup.specific-offsets parameter in the WITH clause. |
|scan.startup.specific-offsets|The start offset of each partition when the scan.startup.mode parameter is set to specific-offsets.|No|String|Example: partition:0,offset:42;partition:1,offset:300|
|scan.startup.timestamp-millis|The timestamp of the start offset when the scan.startup.mode parameter is set to timestamp.|No|Long|Unit: milliseconds.|

To directly configure the Kafka consumer used by the connector, add the `properties` prefix before the configuration parameters of the Kafka consumer and append the configurations to parameters in the WITH clause. The following sample code shows that the Message Queue for Apache Kafka cluster requires Simple Authentication and Security Layer \(SASL\) authentication.

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

For more information about parameters configured for Kafka consumers, see [Consumer Configs](https://kafka.apache.org/documentation/#consumerconfigs).

## Example of reading data from a Message Queue for Apache Kafka topic and inserting the data into another topic

Flink reads data from a topic named source in Message Queue for Apache Kafka and then writes the data to a topic named sink. The data is in the CSV format.

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

## FAQ

-   Q: What is the purpose of the commit offset mechanism in Flink?

    A: Flink commits the read offset to Message Queue for Apache Kafka each time a checkpoint is generated. If checkpointing is disabled or the checkpoint interval is too large, the read offset may not be obtained on Message Queue for Apache Kafka.

-   Q: The network between Flink and Message Queue for Apache Kafka is connected, but error "timeout expired while fetching topic metadata" is still reported. Why?

    A: Data of Message Queue for Apache Kafka may not be read even if the network connection between Flink and Message Queue for Apache Kafka is established. To ensure that they are connected and data of Message Queue for Apache Kafka can be read, you must use the endpoint that is described in the cluster metadata returned by Kafka brokers during bootstrapping. For more information, see [Flink-cannot-connect-to-Kafka](https://www.confluent.io/blog/kafka-client-cannot-connect-to-broker-on-aws-on-docker-etc/).

    To verify that the endpoint is used, perform the following steps:

    1.  Use zkCli.sh or zookeeper-shell.sh to log on to the ZooKeeper service that is used by Message Queue for Apache Kafka.
    2.  Run the `ls /brokers/ids` command to list the IDs of all Kafka brokers.
    3.  Run the `get /brokers/ids/{your_broker_id}` command to view the metadata of Kafka brokers.

        The endpoint is displayed in listener\_security\_protocol\_map.

    4.  Check whether Flink can connect to the endpoint.

        If the endpoint contains a domain name, configure the domain name resolution service for Flink.


