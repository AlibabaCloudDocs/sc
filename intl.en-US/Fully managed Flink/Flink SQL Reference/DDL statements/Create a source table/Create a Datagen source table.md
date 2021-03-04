---
keyword: [Datagen, Datagen source table]
---

# Create a Datagen source table

This topic describes how to create a Datagen source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and sample code used when you create a Datagen source table.

## Introduction to the Datagen source table

A Datagen source table is a built-in connector of the Flink system. This connector periodically generates random data of the type that corresponds to the Datagen source table. If an error is returned when you create a source table of another type, but you cannot determine whether it is caused by a system error or an invalid setting of a parameter in the WITH clause of the source table, you can change the value of connector to datagen and click **Run**. If no error is returned, the Flink system is normal. You must check the settings of parameters in the WITH clause.

## DDL syntax

```
CREATE TABLE datagen_source (
    name VARCHAR,
    score BIGINT
) WITH (
   'connector' = 'datagen'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the source table.|Yes|Set the value to datagen.|
|rows-per-second|The rate at which random data is generated.|No|Default value: 10000 data records per second.|
|fields. \#.length|The length of the generated random string.|No|Default value: 100. The CHAR, VARCHAR, and STRING data types are supported.|
|fields. \#.kind|-   For unbounded data in the data source, fields. \#.kind indicates a random number generator. This is the default value.
-   For bounded data in the data source, fields. \#.kind indicates a sequence generator.

|No|None.|
|fields. \#.max|The maximum value of the random number generator.|No|Only numeric values are supported.|
|fields. \#.min|The minimum value of the random number generator.|No|Only numeric values are supported.|
|fields. \#.start|The start value of the sequence generator.|No|None.|
|fields. \#.end|The end value of the sequence generator.|No|None.|

## Sample code

```
CREATE TEMPORARY table datahub_source(
  name VARCHAR
) WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY table datahub_sink(
  name  VARCHAR  
) with (
  'connector'='datahub',
  'endpoint'='<yourEndpoint>',
  'project'='<yourProject>',
  'topic'='<yourTopic>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>'
);

INSERT INTO datahub_sink
SELECT 
  LOWER(name)
from datahub_source;
```

