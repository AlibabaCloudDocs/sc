---
keyword: [Message Queue for Apache Kafka, result table]
---

# Create a Message Queue for Apache Kafka result table

This topic describes how to create a Message Queue for Apache Kafka result table and provides sample code. It also describes data definition language \(DDL\) statements and parameters in the WITH clause.

**Note:** Message Queue for Apache Kafka connector allows you to write result data to Kafka 0.10 or later.

## Introduction to Message Queue for Apache Kafka

[Message Queue for Apache Kafka](/intl.en-US/Introduction/What is Message Queue for Apache Kafka?.md) is a distributed, high-throughput, and scalable message queue service provided by Alibaba Cloud. This service is widely used in big data applications, such as log collection, monitoring data aggregation, streaming data processing, and both online and offline data analysis.

## DDL syntax

```
create table kafka_sink(  
  user_id BIGINT,
  item_id BIGINT,
  category_id BIGINT,
  behavior STRING,
  ts TIMESTAMP(3)        
) with (
  'connector' = 'kafka',
  'topic' = '<yourTopicName>',
  'properties.bootstrap.servers' = '<yourKafkaBrokers>',
  'format' = 'csv'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Data type|Remarks|
|---------|-----------|--------|---------|-------|
|connector|The type of the result table.|Yes|STRING|Set the value to `kafka`.|
|topic|The topic of the result table.|Yes|STRING|None.|
|properties.bootstrap.servers|The IP addresses or endpoints of Kafka brokers in the Message Queue for Apache Kafka cluster.|Yes|STRING|Format: `host:port,host:port,host:port`. The IP addresses or endpoints are separated by commas \(,\).|
|format|The format that the Flink Kafka connector uses to deserialize messages from Message Queue for Apache Kafka.|Yes|STRING|Valid values:-   csv
-   json
-   avro |
|sink.partitioner|The mapping pattern between Flink and Message Queue for Apache Kafka partitions.|No|STRING|Valid values:-   fixed: Each Flink partition corresponds to only one Kafka partition.
-   round-robin: Data in Flink partitions is distributed to Kafka partitions in turn.
-   Custom FlinkKafkaPartitioner subclass: If the fixed and round-robin patterns do not meet your requirements, you can create a FlinkKafkaPartitioner subclass to customize a mapping pattern, for example, `org.mycompany.MyPartitioner`. |

To directly configure the Kafka producer used by the connector, add the `properties` prefix before Kafka producer configuration parameters and append the Kafka producer configuration to the WITH clause. The following sample code shows that the Message Queue for Apache Kafka cluster requires Simple Authentication and Security Layer \(SASL\) authentication.

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

For more information about parameters configured for Kafka producers, see [Producer Configs](https://kafka.apache.org/documentation/#producerconfigs).

## Example of reading data from a Kafka topic and then inserting the data into another Kafka topic

Flink reads data from a topic named source in Message Queue for Apache Kafka and then writes the data to a topic named sink. The data is in CSV format.

```
CREATE TEMPORARY TABLE kafka_source (
    id INT,
    name STRING,
    age INT
) WITH (
    'connector' = 'kafka',
    'topic' = '<yourTopicName>',
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
    'topic' = '<yourTopicName>',
    'properties.bootstrap.servers' = '<yourKafkaBrokers>',
    'format' = 'csv'
);

INSERT INTO kafka_sink SELECT id, name, age FROM kafka_source;
```

