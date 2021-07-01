---
keyword: [Upsert Kafka, result table]
---

# Create an Upsert Kafka result table

This topic describes how to create an Upsert Kafka result table. It also describes data definition language \(DDL\) statements, parameters in the WITH clause, and sample code used when you create an Upsert Kafka result table.

**Note:** Upsert Kafka connector allows you to write data only to Kafka 0.10 or later.

## Introduction to Upsert Kafka

Upsert Kafka is implemented based on the Upsert Kafka of the Apache Flink community. For more information, see [Upsert Kafka SQL Connector](https://ci.apache.org/projects/flink/flink-docs-master/zh/dev/table/connectors/upsert-kafka.html). Upsert Kafka can consume changelog streams, allow Flink to write INSERT and UPDATE\_AFTER data to Kafka topics as normal Kafka messages, and use the UPSERT method to write DELETE data to Kafka topics. A record with a null value represents a DELETE event. Flink also partitions data based on the value of the primary key column, which ensures that messages with the same primary key are ordered by value. Therefore, UPDATE or DELETE data with the same primary key is written to the same partition.

## DDL syntax

A table named upsert\_kafka\_sink is used as an example in the following DDL statement.

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

**Note:** You must specify a primary key for Upsert Kafka.

## Parameters in the WITH clause

|Parameter|Description|Required|Data type|Remarks|
|---------|-----------|--------|---------|-------|
|connector|The type of the result table.|Yes|STRING|Set the value to `upsert-kafka`.|
|topic|The topic of the result table.|Yes|STRING|None.|
|properties.bootstrap.servers|The IP addresses or endpoints of Kafka brokers.|Yes|STRING|Format: `host:port,host:port,host:port`. The IP addresses or endpoints are separated by commas \(,\).|
|key.format|The format that is used to serialize the Key field of Upsert Kafka messages.|Yes|STRING|Valid formats:-   csv
-   json
-   avro |
|value.format|The format that is used to serialize the Value field of Upsert Kafka messages.|Yes|STRING|Valid formats:-   csv
-   json
-   avro |
|value.fields-include|Specifies the fields in the Value field.|No|String|Valid values:-   ALL: the default value. All the fields of the table schema are included.
-   EXCEPT\_KEY: All the fields of the table schema are included, except the primary key field. |
|properties.\*|The specified Kafka parameters.|No|String|The suffix must match the configuration defined in [Apache Kafka Documentation](https://kafka.apache.org/documentation/#configuration). Flink will remove the properties. prefix and pass the converted parameter name and value to the Kafka client. For example, you can set `'properties.allow.auto.create.topics'` to false to disable the feature of automatic topic creation. **Note:** The values of the 'key.deserializer' and 'value.deserializer' parameters will be overridden by the Kafka configurations. Therefore, we recommend that you do not modify the settings of the two parameters by adding the properties. prefix. for example, `'key.deserializer'` and `'value.deserializer'`. |

## Example

```
--Create a source table for storing data about PVs and UVs. 
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


--Create an Upsert Kafka result table. 
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

--Write the PV and UV data to the result table. 
INSERT INTO pageviews_per_region 
SELECT
  user_region,
  COUNT(*),
  COUNT(DISTINCT user_id)
FROM pageviews
GROUP BY user_region;
```

