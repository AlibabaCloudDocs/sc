---
keyword: [Hologres, dimension table, ]
---

# Create a Hologres dimension table

This topic describes how to create a Hologres dimension table. It also describes the data definition language \(DDL\) statements, parameters in the WITH and CACHE clauses, and sample code used when you create a Hologres dimension table.

## Introduction to Hologres

Hologres is compatible with the PostgreSQL protocol and is closely connected to the big data ecosystem. Hologres allows you to analyze and process petabytes of data in high concurrency and low latency scenarios. Hologres provides an easy method for you to use the existing business intelligence \(BI\) tools to perform multidimensional analysis and explore your business.

## DDL syntax

```
CREATE TABLE hologres_dim(
 id INT,
 len INT,
 content VARCHAR
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourUsername>',
  'password'='<yourPassword>',
  'endpoint'='<yourEndpoint>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the dimension table.|Yes|Set the value to `hologres`.|
|endPoint|hologres endpoint|Yes|For more information, see [Endpoints that are used to access Hologres](/intl.en-US/Manage instances/Endpoints that are used to access Hologres.md).|
|tablename|The name of the table.|Yes|None.|
|dbname|The database name.|Yes|None.|
|username|The username that is used to access the database. You must enter the AccessKey ID of your Alibaba Cloud account.|Yes|None.|
|password|The password that is used to access the database. You must enter the AccessKey secret of your Alibaba Cloud account.|No|None.|

## Parameters in the CACHE clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|cache|The cache policy.|No|Valid values for a Hologres dimension table:-   None: indicates that data is not cached. This is the default cache policy.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, the system searches for the data record in the physical dimension table.

If you use this cache policy, you must configure the cacheSize and cacheTTLMs parameters. |
|cacheSize|The maximum number of data records that can be cached.|No|This parameter is available only if you set the cache parameter to `LRU`. Default value: 10000.|
|cacheTTLMs|The interval at which the cache is refreshed.|No|If the cache parameter is set to LRU, the cacheTTLMs parameter specifies the time before cache entries expire. Cache entries do not expire by default.|
|async|Specifies whether to enable data synchronization in asynchronous mode.|No|Valid values:-   true: Data synchronization in asynchronous mode is enabled.
-   false: Data synchronization in asynchronous mode is disabled. This is the default value. |

## Field type mapping

|Data type of Hologres|Data type of Flink|
|---------------------|------------------|
|INT|INT|
|INT\[\]|ARRAY|
|BIGINT|BIGINT|
|BIGINT\[\]|ARRAY|
|REAL|FLOAT|
|REAL\[\]|ARRAY|
|DOUBLE PRECISION|DOUBLE|
|DOUBLE PRECISION\[\]|ARRAY|
|BOOLEAN|BOOLEAN|
|BOOLEAN\[\]|ARRAY|
|TEXT|VARCHAR|
|TEXT\[\]|ARRAY|
|NUMERIC|DECIMAL|
|DATE|DATE|
|TIMESTAMP WITH TIMEZONE|TIMESTAMP|

**Note:** The Hologress connector supports only the conversion of the Hologress data types that are listed in the table.

## Sample code

```
CREATE TEMPORARY TABLE datagen_source (
   a INT,
   b BIGINT,
   c STRING,
   proctime AS PROCTIME()
) with (
   'connector' = 'datagen'
);

CREATE TEMPORARY TABLE hologres_dim (
   a INT, 
   b VARCHAR, 
   c VARCHAR
) with (
   'connector' = 'hologres',
   ...
);

CREATE TEMPORARY TABLE blackhole_sink (
   a INT,
   b STRING
) with (
   'connector' = 'blackhole'
);

insert into blackhole_sink select T.a,H.b
FROM datagen_source AS T JOIN hologres_dim FOR SYSTEM_TIME AS OF T.proctime AS H ON T.a = H.a;
```

