---
keyword: [blackhole, blackhole result table]
---

# Create a Blackhole result table

This topic describes how to create a blackhole result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and sample code used when you create a blackhole result table.

## Introduction to the blackhole result table

A blackhole result table is a built-in connector of the system. If an error is returned when you create a result table of another type, but you cannot determine whether it is caused by a system error or an invalid setting of a parameter in the WITH clause of the result table, you can change the value of connector to blackhole and click **Run**. If no error is returned, the system is normal. You must check the settings of parameters in the WITH clause.

## DDL syntax

```
create table blackhole_sink(
  name VARCHAR,
  score BIGINT
) with (
  'connector' = 'blackhole'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to blackhole.|

## Sample code

```
CREATE TEMPORARY table datahub_source(
  name VARCHAR
) with (
  'connector'='datahub',
  'endpoint'='<yourEndpoint>',
  'project'='<yourProject>',
  'topic'='<yourTopic>',
  'subId'='<yourSubId>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>',
  'startTime'='2018-06-01 00:00:00'
);

CREATE TEMPORARY table blackhole_sink(
  name  VARCHAR  
) with (
  'connector' = 'blackhole'
);

INSERT INTO blackhole_sink
SELECT 
  LOWER(name)
from datahub_source;
```

