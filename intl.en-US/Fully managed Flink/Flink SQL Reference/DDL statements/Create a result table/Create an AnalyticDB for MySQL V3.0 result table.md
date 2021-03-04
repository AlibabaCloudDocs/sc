---
keyword: [AnalyticDB for MySQL, 3.0, AnalyticDB for MySQL V3.0, result table]
---

# Create an AnalyticDB for MySQL V3.0 result table

This topic describes how to create an AnalyticDB for MySQL V3.0 result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and field type mappings used when you create an AnalyticDB for MySQL V3.0 result table.

## DDL syntax

```
CREATE TABLE adb_sink (
  id INT,
  num BIGINT
) WITH (
  'connector' = 'adb3.0',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = 'jdbc:mysql://<yourNetworkAddress>:<PortId>/<yourDatabaseName>',
  'userName' = '<yourUsername>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to `adb 3.0`.|
|password|The password that is used to access the database.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|url|The Java Database Connectivity \(JDBC\) URL of the AnalyticDB for MySQL database.|Yes|Example: `url='jdbc:mysql://databaseName****-cn-shenzhen-a.ads.aliyuncs.com:10014/databaseName'`.**Note:**

-   For more information about the URLs of AnalyticDB for MySQL databases, see [Query URLs](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Data storage resource registration/Register an AnalyticDB for MySQL instance.md).
-   databaseName is the name of the AnalyticDB for MySQL database. |
|username|The username that is used to access the database.|Yes|None.|
|maxRetryTimes|The number of retries to write data after data writing fails.|No|Default value: 3.|
|batchSize|The number of data records that can be written at a time.|No|Default value: 5000.|
|flushIntervalMs|The time interval at which the cache is cleared.|No|Default value: 0. Unit: milliseconds. This value indicates that cache flushing is disabled. If this parameter is set to 2000, all cached data is written to the result table when the number of input data records does not reach the value specified by the bufferSize parameter within 2,000 milliseconds.|

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
CREATE TEMPORARY TABLE datagen_source (
  `name` VARCHAR,
  `age` INT
) WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE adb_sink (
  `name` VARCHAR,
  `age` INT
) WITH (
  'connector' = 'adb3.0',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = '<yourUrl>',
  'userName' = '<yourUsername>'
);

INSERT INTO adb_sink
SELECT  * FROM datagen_source;
```

