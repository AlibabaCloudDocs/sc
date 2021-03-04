---
keyword: [Message Queue for Apache Kafka, source table]
---

# Create a Message Queue for Apache Kafka source table

This topic describes how to create a Message Queue for Apache Kafka source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and an example used when you create a Message Queue for Apache Kafka source table.

## Introduction to the Message Queue for Apache Kafka source table

Message Queue for Apache Kafka is a distributed, high-throughput, and scalable message queue service provided by Alibaba Cloud. This service is widely used in big data applications, such as log collection, monitoring data aggregation, streaming data processing, and both online and offline data analysis.

## DDL syntax

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

## Parameters in the WITH clause

|Parameter|Description|Required|Data type|Remarks|
|---------|-----------|--------|---------|-------|
|connector|The type of the source table.|Yes|STRING|Set the value to `kafka`.|
|topic|The topic of the source table.|Yes|STRING|None.|
|properties.bootstrap.servers|The IP addresses or endpoints of Kafka brokers in the Message Queue for Apache Kafka cluster.|Yes|STRING|Format: `host:port,host:port,host:port`. The IP addresses or endpoints are separated by commas \(,\).|
|properties.group.id|The ID of a Kafka consumer group.|Yes|STRING|None.|
|format|The format that the Flink Kafka connector uses to deserialize messages from Message Queue for Apache Kafka.|Yes|STRING|Valid values:-   csv
-   json
-   avro
-   debezium-json
-   canal-json |
|scan.startup.mode|The start consumer offset for Kafka messages.|No|STRING|Valid values:-   group-offsets: reads data by group. This is the default value.
-   earliest-offset: reads data from the earliest partition of Message Queue for Apache Kafka.
-   latest-offset: reads data from the latest offset of Message Queue for Apache Kafka.
-   timestamp: reads data from a specified time point of Message Queue for Apache Kafka.

You must specify the scan.startup.timestamp-millis parameter in the WITH clause.

-   specific-offsets: reads data from a specified partition of Message Queue for Apache Kafka at a specified offset.

You must specify the scan.startup.specific-offsets parameter in the WITH clause. |
|scan.startup.specific-offsets|The start offset of each partition when the scan.startup.mode parameter is set to specific-offsets.|No|STRING|Example: partition:0,offset:42;partition:1,offset:300|
|scan.startup.timestamp-millis|The timestamp of the start offset when the scan.startup.mode parameter is set to timestamp.|No|LONG|Unit: milliseconds.|

To directly configure the Kafka consumer used by the connector, add the `properties` prefix before Kafka consumer configuration parameters and append the Kafka consumer configuration to parameters in the WITH clause. The following sample code shows that the Message Queue for Apache Kafka cluster requires Simple Authentication and Security Layer \(SASL\) authentication.

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

## Example of reading data from a Kafka topic and inserting the data into another topic

Flink reads data from a topic named source in Message Queue for Apache Kafka and then writes the data to a topic named sink. The data is in the CSV format.

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

## FAQ

Q: What are the functions of the commit offset mechanism in Flink?

A: Flink uses commitOffsetOnCheckpointing by default. The commit offset policy that you configured does not take effect. After checkpoint generation is enabled, Flink commits the offset consumed in the current partition to Kafka each time a checkpoint is generated. This way, data is consumed from the offset committed from the last checkpoint during job restoration. This ensures exactly-once processing of streaming data. If the checkpoint interval exceeds the specified upper limit, Kafka may fail to query the consumer offset.

