# Create an AnalyticDB for MySQL V3.0 result table

This topic describes how to create an AnalyticDB for MySQL V3.0 result table in Realtime Compute for Apache Flink. This topic also describes the parameters in the WITH clause used when you create an AnalyticDB for MySQL V3.0 result table.

**Note:**

-   You cannot register AnalyticDB for MySQL V3.0 storage resources in the Realtime Compute for Apache Flink console to store result tables.
-   This topic applies only to `Blink 3.3.0` and later.
-   For more information about how to create an AnalyticDB for MySQL V2.0 result table, see [Create an AnalyticDB for MySQL V2.0 result table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Create an AnalyticDB for MySQL V2.0 result table.md).
-   You are allowed to define an auto-increment primary key for an AnalyticDB for MySQL V3.0 database. If you want to use the auto-increment primary key, do not declare the auto-increment field in a data definition language \(DDL\) statement. For example, if you use ID as an auto-increment field, do not declare the ID field in the DDL statement. When a row of output data is written to the AnalyticDB for MySQL V3.0 database, the value for the auto-increment field is automatically filled.

## DDL syntax

In Realtime Compute for Apache Flink, you can use AnalyticDB for MySQL V3.0 to store output data. The following code shows an example:

```
CREATE TABLE rds_output (
id INT,
len INT,
content VARCHAR,
PRIMARY KEY(id,len)
) WITH (
type='ADB30',
url='jdbc:mysql://<yourNetworkAddress>:<PortId>/<yourDatabaseName>',
tableName='<yourDatabaseTableName>',
userName='<yourDatabaseUserName>',
password='<yourDatabasePassword>'
);
```

## Implementation

Realtime Compute for Apache Flink writes data to an AnalyticDB for MySQL V3.0 result table in two steps:

1.  Converts each row of output data to a line of SQL statement.
2.  Writes and executes the SQL statement in the destination database.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the connector.|Yes|Set the value to `ADB30`.|
|url|The Java Database Connectivity \(JDBC\) URL of the database.|Yes|The URL of the AnalyticDB for MySQL database, for example, `url='jdbc:mysql://databaseName****-cn-shenzhen-a.ads.aliyuncs.com:10014/databaseName'`.**Note:**

-   For more information about how to access an AnalyticDB for MySQL database, see [Query a URL](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Data storage resource registration/Register an AnalyticDB for MySQL instance.md).
-   databaseName is the name of the AnalyticDB for MySQL database. |
|tableName|The name of the table.|Yes|None.|
|username|The username that is used to access the database.|Yes|None.|
|password|The password that is used to access the database.|Yes|None.|
|maxRetryTimes|The maximum number of retries for writing data to the table.|No|Default value: 3.|
|bufferSize|The maximum number of data records that can be stored in the buffer before data deduplication is triggered.|No|Default value: 1000. This value indicates that duplicates are removed when the number of input data records reaches 1,000.**Note:** This parameter is valid only after you specify the primary key. |
|batchSize|The number of data records that can be written at a time.|No|Default value: 1000.**Note:** This parameter is valid only after you specify the primary key. |
|flushIntervalMs|The time interval at which the cache is cleared.|No|Default value: 3000. Unit: milliseconds. This value indicates that all the cached data is written to the result table if the number of input data records does not reach the batchSize value within 3,000 milliseconds.|
|ignoreDelete|Specifies whether to ignore delete operations.|No|Default value: false. This value indicates that the delete operations are supported.|
|replaceMode|Specifies whether to use the REPLACE INTO statement to insert data into the table.|No|Valid values:-   true: The REPLACE INTO statement is used to insert data into the table. This value is the default value.
-   false: The INSERT INTO ON DUPLICATE KEY statement is used to insert data into the table.

**Note:** This parameter is valid only when the following conditions are met:

-   The Blink version is 3.X or later.
-   The AnalyticDB for MySQL version must be 3.1.3.5 or later. |
|reserveMilliSecond|Specifies whether to reserve the millisecond component in a value of the TIMESTAMP data type.|No|Default value: false. This value indicates that the millisecond component is not reserved.|

