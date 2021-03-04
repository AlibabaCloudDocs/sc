---
keyword: [AnalyticDB for MySQL, 3.0, AnalyticDB for MySQL V3.0, dimension table]
---

# Create an AnalyticDB for MySQL V3.0 dimension table

This topic describes how to create an AnalyticDB for MySQL V3.0 dimension table. It also describes the data definition language \(DDL\) statements, parameters in the WITH and CACHE clauses, and field type mappings used when you create an AnalyticDB for MySQL V3.0 dimension table.

## DDL syntax

```
CREATE TABLE adb30_dim (
    id1 INT,
    id2 VARCHAR
) WITH (
    'connector' = 'adb3.0',
    'password' = '<yourPassword>',
    'tableName' = '<yourTablename>',
    'url' = '<yourUrl>',
    'userName' = '<yourUsername>',
    'cache' = 'ALL',
    'cacheSize' = '500'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the dimension table.|Yes|Set the value to `adb3.0`.|
|password|The password that is used to access the database.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|url|The URL of the database.|Yes|The URL of the AnalyticDB for MySQL V3.0 database in a VPC.|
|username|The username that is used to access the database.|Yes|None.|
|maxRetryTimes|The number of retries for writing data to the table.|No|Default value: 3.|
|CACHE|The parameters in the CACHE clause. These parameters are used to configure the cache policy, cache size, and cache timeout period.|No|For more information, see [Parameters in the CACHE clause](#section_rd3_uc3_lqr).|

## Parameters in the CACHE clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|cache|The cache policy.|No|Valid values for an AnalyticDB for MySQL V3.0 dimension table: -   None: indicates that data is not cached. This is the default cache policy.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, the system searches for the data record in the physical dimension table.
-   ALL: indicates that all data in the dimension table is cached. Before a job starts to run, the system loads all the data in the dimension table to the cache. If you need to retrieve data from the dimension table, the system searches the cache for all subsequent queries in the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system loads all data in the cache again after cache entries expire.

If the amount of data of a remote table is small and a large number of missing keys exist, we recommend that you set this parameter to ALL. \(The source table and dimension table cannot be associated based on the ON clause.\)


**Note:**

-   You must specify the cacheSize parameter if the cache parameter is set to ALL or LRU.
-   If you set the cache parameter to ALL, pay attention to the memory of the JOIN operator to prevent out of memory \(OOM\).
-   If the cache parameter is set to ALL, you must increase the memory of the node for joining tables because the system asynchronously loads data from the dimension table. The increased memory size is two times that of the remote table. |
|cacheSize|The cache size, which specifies the number of rows that can be cached.|No|The setting of cacheSize is related to the value of cache.-   If the cache parameter is set to None, you do not need to specify the cacheSize parameter. This parameter is empty by default.
-   If the cache parameter is set to LRU, you must specify the cacheSize parameter.
-   If the cache parameter is set to ALL, you must specify the cacheSize parameter. |
|cacheTTLMs|The cache timeout period. Unit: milliseconds.|No|The setting of cacheTTLMs is related to the value of cache.-   If the cache parameter is set to ALL, the cacheTTLMs parameter specifies the interval at which the system refreshes the cache. The default value is 10000.
-   If the cache parameter is set to LRU, the cacheTTLMs parameter specifies the cache timeout period. The default value is 10000. |

## Field type mapping

|Data type of AnalyticDB for MySQL V3.0|Data type of Flink|
|--------------------------------------|------------------|
|BOOLEAN|BOOLEAN|
|TINYINT|INT|
|SMALLINT|INT|
|INT|INT|
|BIGINT|BIGINT|
|DOUBLE|DOUBLE|
|VARCHAR|VARCHAR|
|DATETIME|TIMESTAMP|
|DATE|DATE|

## Sample code

```
CREATE TEMPORARY TABLE datagen_source(
  a INT,
  b VARCHAR,
  c STRING,
  `proctime` AS PROCTIME()
) with (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE adb_dim (
  a INT, 
  b VARCHAR, 
  c VARCHAR
) with (
  'connector' = 'adb3.0',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = '<yourUrl>',
  'userName' = '<yourUsername>'
);

CREATE TEMPORARY TABLE blackhole_sink(
  a INT,
  b VARCHAR
) with (
  'connector' = 'blackhole'
);

insert into blackhole_sink select T.a,H.b
FROM datagen_source AS T JOIN adb_dim FOR SYSTEM_TIME AS OF T.proctime AS H ON T.a = H.a;
```

