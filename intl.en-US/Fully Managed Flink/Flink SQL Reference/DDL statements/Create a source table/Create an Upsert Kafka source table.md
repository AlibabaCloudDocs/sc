---
keyword: [Upsert Kafka, source table]
---

# Create an Upsert Kafka source table

This topic describes how to create an Upsert Kafka source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and example code used when you create an Upsert Kafka source table.

## Introduction to Upsert Kafka

Upsert Kafka is implemented based on the upsert-kafka connector of the Apache Flink community. For more information, see [Upsert Kafka SQL connector](https://ci.apache.org/projects/flink/flink-docs-master/zh/dev/table/connectors/upsert-kafka.html). Upsert Kafka produces a changelog stream and allows you to read data from and write data to Kafka topics in the upsert fashion. Each data record in the changelog stream represents an update or delete event. Each data record is processed in the following way:

-   If a Kafka topic contains a key that is the same as that in a data record, the value in the data record overwrites the value of the key. The data record is interpreted as UPDATE.
-   If a Kafka topic does not contain such a key, the value in the data record is inserted into the Kafka topic. The data record is interpreted as INSERT.
-   If the value of the key in a data record is null, the data record is interpreted as DELETE.

## DDL syntax

The following syntax uses the pageviews\_per\_region table as an example.

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

**Note:** You must declare the primary key when you create an Upsert Kafka table.

## Parameters in the WITH clause

|Parameter|Description|Required|Data type|Remarks|
|---------|-----------|--------|---------|-------|
|connector|The type of the source table.|Yes|String|Set the value to `upsert-kafka`.|
|topic|The topic of the source table.|Yes|String|None.|
|properties.bootstrap.servers|The IP addresses or endpoints of Kafka brokers.|Yes|String|Format: `host:port,host:port,host:port`. The IP addresses or endpoints are separated by commas \(,\).|
|key.format|The format used to deserialize the Key field of Upsert Kafka messages.|Yes|String|The following formats are supported:-   csv
-   json
-   avro |
|value.format|The format used to deserialize the Value field of Upsert Kafka messages.|Yes|String|The following formats are supported:-   csv
-   json
-   avro |
|value.fields-include|Specifies the fields that appear in the Value field.|Yes|String|Valid values:-   ALL: indicates all fields in the schema, including the primary key field. This is the default value.
-   EXCEPT\_KEY: indicates all fields in the schema, excluding the primary key field. |
|key.fields-prefix|Specifies a prefix for all fields of the primary key to prevent a name conflict between the Key and Value fields.|No|String|The prefix is empty by default. If a prefix is defined, the table schema and key.fields use the prefix. When you construct the data type for a Kafka Key field of a specific format, the prefix of the primary key name is deleted and the remaining part is used as its name.

**Note:** The key.fields-prefix parameter takes effect only when you set value.fields-include to EXCEPT\_KEY. |
|properties.\*|Specifies Kafka parameters.|No|String|The suffix must match the parameter name defined in the [Apache Kafka documentation](https://kafka.apache.org/documentation/#configuration). Flink automatically removes the properties.prefix and passes the converted parameter name and value to the Kafka client. For example, you can set `properties.allow.auto.create.topics` to false to disable the feature of automatic topic creation. **Note:** You cannot use this parameter to specify the `key.deserializer` or `value.deserializer` parameter because Flink will rewrite the values of the two parameters. |

## Example

```
-- Create an Upsert Kafka source table. 
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

-- Create a Kafka result table. 
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

-- Write data from the Upsert Kafka source table to the Kafka result table. 
INSERT INTO pageviews_per_region_db
SELECT user_region, pv, uv FROM pageviews_per_region;
```

