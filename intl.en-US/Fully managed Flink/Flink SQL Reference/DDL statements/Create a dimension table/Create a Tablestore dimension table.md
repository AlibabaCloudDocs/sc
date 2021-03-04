---
keyword: [Tablestore, dimension table]
---

# Create a Tablestore dimension table

This topic describes how to create a Tablestore dimension table. It also describes the data definition language \(DDL\) statements and parameters in the WITH and CACHE clauses used when you create a Tablestore dimension table.

## Introduction to Tablestore

Tablestore is a distributed NoSQL database service that is built on the Apsara distributed operating system of Alibaba Cloud. Tablestore adopts sharding and load balancing technologies to scale out services and handle concurrent transactions. You can use Tablestore to store and query large amounts of structured data in real time.

## DDL syntax

```
CREATE TABLE ots_dim (
   id int,
   len int,
   content STRING
) WITH (
   'connector'='ots',
   'endPoint'='<yourEndpoint>',
   'instanceName'='<yourInstanceName>',
   'tableName'='<yourTableName>',
   'accessId'='<yourAccessId>',
   'accessKey'='<yourAccessKey>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the dimension table.|Yes|Set the value to `ots`.|
|instanceName|The name of the instance.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|endPoint|The endpoint of the instance.|Yes|None.|
|accessId|The AccessKey ID that is used to access the database.|Yes|None.|
|accessKey|The AccessKey secret that is used to access the database.|Yes|None.|
|retryIntervalMs|The retry interval.|No|Unit: milliseconds. Default value: 1000.|
|maxRetryTimes|The maximum number of retries.|No|Default value: 100.|
|connectTimeout|The timeout period for a connector to connect to Tablestore.|No|Unit: milliseconds. Default value: 30000.|
|socketTimeout|The socket timeout period for a connector to connect to Tablestore.|No|Unit: milliseconds. Default value: 30000.|

## Parameters in the CACHE clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|cache|The cache policy.|Valid values for a Tablestore dimension table: -   None: indicates that data is not cached. This is the default cache policy.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, the system searches for the data record in the physical dimension table.

If you use this cache policy, you must configure the cacheSize and cacheTTLMs parameters. |
|cacheSize|The maximum number of data records that can be cached.|This parameter is available only if you set the cache parameter to LRU. Default value: 10000.|
|cacheTTLMs|The cache timeout period.|This parameter is available only if you set the cache parameter to LRU.|

## FAQ

Q: What do I do if no data can be queried when I join a dimension table with another table?

A: Check whether the schema type and name in the DDL statement in each table is the same as those in its physical table.

