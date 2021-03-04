---
keyword: [ApsaraDB RDS for MySQL, ApsaraDB RDS, MySQL]
---

# Create an ApsaraDB RDS for MySQL dimension table

This topic describes how to create an ApsaraDB RDS for MySQL dimension table. It also describes the data definition language \(DDL\) statements, parameters in the WITH and CACHE clauses, field type mappings, and sample code used when you create an ApsaraDB RDS for MySQL dimension table.

## Syntax

```
CREATE TABLE rds_dim (
  id1 INT,
  id2 VARCHAR
) WITH (
  'connector' = 'rds',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = '<yourUrl>',
  'userName' = '<yourUsername>'
  'cache' = 'ALL'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the dimension table.|Yes|Set the value to `rds`.|
|password|The password that is used to access the database.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|url|The URL of the ApsaraDB RDS for MySQL database in a VPC.|Yes|For more information, see [View and change the internal and public endpoints and port numbers of an ApsaraDB RDS for MySQL instance](/intl.en-US/RDS MySQL Database/Database connection/View and change the internal and public endpoints and port numbers of an ApsaraDB
         RDS for MySQL instance.md).|
|userName|The username that is used to access the database.|Yes|None.|
|maxRetryTimes|The number of retries for writing data to the table.|No|Default value: 3.|
|CACHE|The parameters in the CACHE clause. These parameters are used to configure the cache policy, cache size, and cache timeout period.|No|For more information, see [Parameters in the CACHE clause](#section_5g6_dkf_nd2).|

## Parameters in the CACHE clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|cache|The cache policy.|No|Valid values for an ApsaraDB RDS or Distributed Relational Database Service \(DRDS\) dimension table: -   None: indicates that data is not cached. This is the default cache policy.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, the system searches for the data record in the physical dimension table.
-   ALL: indicates that all data in the dimension table is cached. Before a job starts to run, the system loads all the data in the dimension table to the cache. If you need to retrieve data from the dimension table, the system searches the cache for all subsequent queries in the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system loads all data in the cache again after cache entries expire.

If the data amount of a remote table is small and a large number of missing keys exist, we recommend that you set this parameter to ALL. \(The source table and dimension table cannot be associated based on the ON clause.\)


**Note:**

-   You must specify the cacheSize parameter if the cache parameter is set to ALL or LRU.
-   If you set the cache parameter to ALL, pay attention to the memory of the JOIN operator to prevent out of memory \(OOM\).
-   If the cache parameter is set to ALL, you must increase the memory of the node for joining tables because the system asynchronously loads data from the dimension table. The increased memory size is two times that of the remote table. |
|cacheSize|The maximum number of data records that can be cached.|No|This parameter is empty by default. This indicates that this parameter is not configured. You must specify this parameter if the cache parameter is set to ALL or LRU.|
|cacheTTLMs|The cache timeout period. Unit: milliseconds.|No|This parameter is available only if you set the cache parameter to `LRU`. If you set the cache parameter to `ALL`, the cacheTTLMs parameter specifies the interval at which the system refreshes the cache. The default value is 10s.|

## Field type mapping

|Data type of ApsaraDB RDS for MySQL|Data type of Flink|
|-----------------------------------|------------------|
|BOOLEAN|BOOLEAN|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|TINYINT UNSIGNED|SMALLINT|
|INT|INT|
|SMALLINT UNSIGNED|INT|
|BIGINT|BIGINT|
|INT UNSIGNED|BIGINT|
|BIGINT UNSIGNED|DECIMAL\(20,0\)|
|FLOAT|FLOAT|
|DECIMAL|DECIMAL|
|DOUBLE|DOUBLE|
|DATE|DATE|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|VARBINARY|VARBINARY|

## Sample code

```
CREATE TEMPORARY TABLE datagen_source(
  a INT,
  b BIGINT,
  c STRING,
  `proctime` AS PROCTIME()
) with (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE rds_dim (
  a INT, 
  b VARCHAR, 
  c VARCHAR
) with (
  'connector' = 'rds',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = 'jdbc:mysql://xxx',
  'userName' = '<yourUsername>'
);

CREATE TEMPORARY TABLE blackhole_sink(
  a INT,
  b STRING
) with (
  'connector' = 'blackhole'
);

insert into blackhole_sink select T.a,H.b
FROM datagen_source AS T JOIN rds_dim FOR SYSTEM_TIME AS OF T.`proctime` AS H ON T.a = H.a;
```

