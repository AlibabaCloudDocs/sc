---
keyword: Phoenix5 result table
---

# Create a Phoenix5 result table

This topic describes how to create a Phoenix5 result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and sample code used when you create a Phoenix5 result table.

## DDL syntax

```
create table phoenix5_sink (
  `STATE` varchar,
  CITY varchar,
  POPULATION BIGINT,
  PRIMARY KEY (`STATE`, CITY)
) WITH (
  'connector' = 'phoenix5',
  'serverUrl' = '<yourserverUrl>',
  'tableName' = '<yourTableName>',
  'batchsize' = '25'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Default value: `phoenix 5`.|
|serverUrl|The URL of the Phoenix5 query server.-   If Phoenix5 is created in a cluster, the value of this parameter is the URL of Server Load Balancer \(SLB\).
-   If Phoenix5 is created on a single server, the value of this parameter is the URL of the server.

|Yes|You must enable the HBase SQL service in an ApsaraDB for HBase instance.|
|tableName|The name of the Phoenix5 table.|Yes|None.|
|batchSize|The number of data records that can be written at a time.|No|Default value: 20.|

## Sample code

The following sample code shows how to create a Phoenix5 result table in a Flink job.

```
CREATE TEMPORARY table datagen_source (
  `id` varchar,
  `name` varchar,
  `age` varchar,
  `birthday` varchar 
) WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY table phoenix5_sink (
  `id` varchar,
  `name` varchar,
  `age` varchar,
  `birthday` varchar,
  primary key (id)
) WITH (
  'connector' = 'phoenix5',
  'serverurl' = '<yourserverUrl>',
  'tablename' = '<yourTableName>',
  'batchsize' = '25'
);

INSERT INTO phoenix5_sink
  SELECT  `id` ,`name` , `age` ,`birthday` 
FROM datagen_source;
```

