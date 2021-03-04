---
keyword: [MongoDB, result table]
---

# Create an ApsaraDB for MongoDB result table

This topic describes how to create an ApsaraDB for MongoDB result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and sample code used when you create an ApsaraDB for MongoDB result table.

**Note:** You can use MongoDB tables only as result tables.

## DDL syntax

```
CREATE TABLE mongodb_sink(
   id INT, 
   number INT
) with (
   'connector' = 'mongodb',
   'database' = '<yourDatabase>',
   'collection' = '<yourCollection>', 
   'uri' = '<yourUri>',
   'maxConnectionIdleTime' = '<yourMaxConnectionIdleTime>',  
   'batchSize' = '1024'  
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to `mongodb`.|
|database|The name of the database.|Yes|None.|
|collection|The set of result table data.|Yes|None.|
|uri|The URI of the ApsaraDB for MongoDB database.|Yes|Example: `mongodb://123@dds-/admin? replicaSet=mgset-32966591`.|
|maxConnectionIdleTime|The connection timeout period.|No|Default value: 60000.|
|batchSize|The number of data records that can be written at a time.|No|Default value: 1024.|

## Sample code

```
CREATE TEMPORARY TABLE datagen_source (
   v INT, 
   p INT
) with (
   'connector' = 'datagen'
);

CREATE TEMPORARY TABLE mongodb_sink(
   id INT, 
   number INT
) with (
   'connector'='mongodb',
   'database' = '<yourDatabase>',
   'collection' = '<yourCollection>', 
   'uri'='<yourUri>'
);

INSERT INTO mongodb_sink 
   SELECT v, p
FROM datagen_source;
```

