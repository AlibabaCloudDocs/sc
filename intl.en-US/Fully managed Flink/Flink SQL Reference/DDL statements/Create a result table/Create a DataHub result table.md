---
keyword: [DataHub, result table]
---

# Create a DataHub result table

This topic describes how to create a DataHub result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and field type mappings used when you create a DataHub result table.

## Introduction to DataHub

DataHub is a real-time data distribution platform designed to process streaming data. You can publish and subscribe to applications for streaming data in DataHub and distribute the data to other platforms. DataHub allows you to analyze streaming data and build applications based on the streaming data. Flink can use the streaming data that is stored in DataHub as output data.

## DDL syntax

```
create table datahub_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT
)with(
  'connector'='datahub',
  'endpoint'='<endPoint>',
  'project'='<yourProjectName>',
  'topic'='<yourTopicName>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>',
  'batchCount'='500',
  'batchSize'='512000',
  'flushInterval'='5000'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to `datahub`.|
|endPoint|The endpoint of DataHub.|Yes|For more information, see [List of DataHub endpoints](https://help.aliyun.com/document_detail/158778.html?spm=a2c4g.11186623.6.547.77a91fd1eveQrC).|
|accessId|The AccessKey ID that is used to access the database.|Yes|None.|
|accessKey|The AccessKey secret that is used to access the database.|Yes|None.|
|project|A project in DataHub.|Yes|None.|
|topic|A topic of the project.|Yes|None.|
|retryTimeout|The maximum retry duration.|No|Default value: 180000. Unit: milliseconds.|
|retryInterval|The retry interval.|No|Default value: 1000. Unit: milliseconds.|
|batchCount|The maximum number of data records that can be written at a time.|No|Default value: 500.|
|batchSize|The size of data that can be written at a time.|No|Default value: 512000. Unit: bytes.|
|flushInterval|The timeout period for writing data.|No|Default value: 5000. Unit: milliseconds.|
|partitionBy|Before Flink writes data to a sink node, it performs a hash partitioning based on the value of this parameter and distributes the data to the sink node.|No|This parameter is empty by default. The value of this parameter is randomly allocated.|
|hashFields|The column names. After column names are specified, the values of the columns with the same name are written to the same shard.|No|Default value: Null. This value indicates that you can specify multiple column names for random write operations. The column names are separated by commas \(,\), for example, `hashFields=a,b`.|

## Field type mapping

The following table lists the data type mappings between DataHub and Flink. We recommend that you declare the data type mappings in a DDL statement.

|Data type of DataHub|Data type of Flink|
|--------------------|------------------|
|STRING|VARCHAR|
|TIMESTAMP|BIGINT|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INTEGER|INTEGER|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DECIMAL|DECIMAL|

## Sample code

```
CREATE TEMPORARY table datahub_source(
  name VARCHAR
) with (
  'connector'='datahub',
  'endpoint'='<endPoint>',
  'project'='<yourProjectName>',
  'topic'='<yourTopicName>',
  'subId'='<yourSubId>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>',
  'startTime'='2018-06-01 00:00:00'
);

CREATE TEMPORARY table datahub_sink(
  name varchar
)with(
  'connector'='datahub',
  'endpoint'='<endPoint>',
  'project'='<yourProjectName>',
  'topic'='<yourTopicName>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>',
  'batchSize'='512000',
  'batchCount'='500'
);

INSERT INTO datahub_sink
SELECT 
  LOWER(name)
from datahub_source;
```

