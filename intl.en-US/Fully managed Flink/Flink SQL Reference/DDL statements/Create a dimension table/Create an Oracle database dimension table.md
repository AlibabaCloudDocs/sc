---
keyword: create an Oracle database dimension table
---

# Create an Oracle database dimension table

This topic describes how to create an Oracle database dimension table. It also describes the data definition language \(DDL\) statements, field type mappings, and sample code used when you create an Oracle database dimension table.

## DDL syntax

```
CREATE TABLE oracle_dim(
  employee_id BIGINT,
  phone_number BIGINT,
  dollar DOUBLE,
  PRIMARY KEY (employee_id)
) WITH (
  'connector' = 'oracle_dim',
  'url' = '<yourUrl>',
  'userName' = '<yourUserName>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTableName>',
  'cache' = 'ALL'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|Basic configuration|connector|The type of the dimension table.|Yes|Set the value to oracle\_dim.|
|url|The Java Database Connectivity \(JDBC\) URL of the Oracle database.|Yes|`jdbc:oracle:thin:@ip:port:sid`|
|userName|The username that is used to access the database.|Yes|None.|
|password|The password that is used to access the database.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|maxRetryTimes|The maximum number of retries for reading data from the dimension table.|No|Default value: 10.|
|Cache configuration|cache|The cache policy.|No|Valid values for an Oracle database dimension table: -   None: indicates that data is not cached. This is the default cache policy.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, the system searches for the data record in the physical dimension table.

If you use this cache policy, you must configure the cacheSize and cacheTTLMs parameters.

-   ALL: indicates that all data in the dimension table is cached. Before a job starts to run, the system loads all the data in the dimension table to the cache. If you need to retrieve data from the dimension table, the system searches the cache for all subsequent queries in the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system loads all data in the cache again after cache entries expire.

If the data amount of a remote table is small and a large number of missing keys exist, we recommend that you set this parameter to ALL. \(The source table and dimension table cannot be associated based on the ON clause.\)

If you use this cache policy, you must configure the cacheTTLMs and cacheReloadTimeBlackList parameters.

**Note:** If the cache parameter is set to ALL, you must increase the memory of the node for joining tables because the system asynchronously loads data from the dimension table. The increased memory size is two times that of the remote table. |
|cacheSize|The cache size, which specifies the number of rows that can be cached.|No|This parameter is available only if you set the cache parameter to LRU. Default value: 10000.|
|cacheTTLMs|The cache timeout period. Unit: milliseconds.|No|-   If you set the cache parameter to LRU, this parameter specifies the interval at which the system refreshes the cache. By default, the cache is not refreshed.
-   If you set the cache parameter to ALL, this parameter specifies the interval at which the system refreshes the cache. By default, the cache is not refreshed. |
|cacheReloadTimeBlackList|The time periods during which the cache is not refreshed. This parameter takes effect when the cache parameter is set to ALL. The cache is not refreshed during the time periods that you specify for this parameter. This parameter is useful for massive online promotional events such as Double 11.|No|This parameter is empty by default. The following example shows the format of the values: '2017-10-24 14:00 -\> 2017-10-24 15:00, 2017-11-10 23:30 -\> 2017-11-11 08:00'. Delimiters that you can use for the parameter values: -   Separate the time periods with commas \(,\).
-   Separate the start time and end time of each time period with a hyphen and a greater-than sign \(-\>\). |
|maxJoinRows|The maximum number of data records in the right table that can be joined with a data record in the left table for a one-to-many table join.|No|Default value: 1024.**Note:** If a large number of data records are involved in one-to-many table joins, you must adjust the memory of the cache. The cacheSize parameter restricts the number of keys in the left table. If a data record in the left table corresponds to a large number of data records in the right table, the performance of streaming data tasks significantly deteriorates. |

## Field type mapping

|Data type of an Oracle database|Data type of Flink|
|-------------------------------|------------------|
|-   CHAR
-   VARCHAR
-   VARCHAR2

|VARCHAR|
|FLOAT|DOUBLE|
|NUMBER|BIGINT|
|DECIMAL|DECIMAL|

## Sample code

```
CREATE TEMPORARY TABLE oracle_source (
  employee_id BIGINT,
  employee_name VARCHAR,
  employee_age INT
) WITH (
  'connector'='datagen'
);


CREATE TEMPORARY TABLE oracle_dim (
  employee_id BIGINT,
  phone_number BIGINT,
  dollar DOUBLE,
  PRIMARY KEY (employee_id)
) WITH (
  'connector' = 'oracle_dim',
  'url' = '<yourUrl>',
  'userName' = '<yourUserName>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTableName>',
  'cache' = 'ALL'
);

CREATE TEMPORARY TABLE oracle_sink (
  employee_id BIGINT,
  phone_number BIGINT,
  employee_name VARCHAR
) WITH (
  'connector' = 'oracle',
  'url' = '<yourUrl>',
  'userName' = '<yourUserName>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTableName>'
);

INSERT INTO oracle_sink
SELECT t.employee_id, w.phone_number, t.employee_name
FROM oracle_source as t JOIN oracle_dim FOR SYSTEM_TIME AS OF PROCTIME() as w
ON t.employee_id = w.employee_id;
```

