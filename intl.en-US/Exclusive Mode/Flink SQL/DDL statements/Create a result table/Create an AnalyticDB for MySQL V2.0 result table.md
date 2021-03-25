# Create an AnalyticDB for MySQL V2.0 result table

This topic describes how to create an AnalyticDB for MySQL V2.0 result table. It also describes the mapping between the field data types of AnalyticDB for MySQL and Realtime Compute for Apache Flink.

**Note:**

-   This topic applies only to Blink 1.4.5 and later.
-   You are allowed to define an auto-increment primary key for an AnalyticDB for MySQL V2.0 database. If you want to use the auto-increment primary key, do not declare the auto-increment field in a data definition language \(DDL\) statement. For example, if you use ID as an auto-increment field, do not declare the ID field in the DDL statement. When a row of output data is written to the ApsaraDB RDS for MySQL database, the value for the auto-increment field is automatically filled.

## Introduction to AnalyticDB for MySQL

AnalyticDB for MySQL is a real-time online analytical processing \(OLAP\) service that is developed by Alibaba Cloud. It is a high concurrency service that has excellent performance in processing large amounts of data. AnalyticDB for MySQL allows you to query and analyze hundreds of billions of data records within milliseconds.

## DDL syntax

**Note:** For more information about how to create an AnalyticDB for MySQL V3.0 result table, see [Create an AnalyticDB for MySQL V3.0 result table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Create an AnalyticDB for MySQL V3.0 result table.md).

In Realtime Compute for Apache Flink, you can use AnalyticDB for MySQL V2.0 to store output data. The following code shows an example:

```
CREATE TABLE stream_test_hotline_agent (
id INTEGER,
len BIGINT,
content VARCHAR,
PRIMARY KEY(id)
) WITH (
type='ads',
url='yourDatabaseURL',
tableName='<yourDatabaseTableName>',
userName='<yourDatabaseUserName>',
password='<yourDatabasePassword>',
batchSize='20'
);
```

**Note:**

-   We recommend that you use the storage registration feature. For more information, see [Register an AnalyticDB for MySQL instance](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Data storage resource registration/Register an AnalyticDB for MySQL instance.md).
-   The primary key that is declared in an AnalyticDB for MySQL result table must be consistent with that in an AnalyticDB for MySQL database. The primary key is case-sensitive. Inconsistency may cause the "array index out of bounds" error.

## Parameters in the WITH clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|type|The type of the result table.|Set the value to ads.|
|url|The Java Database Connectivity \(JDBC\) URL of a database.|The URL of the AnalyticDB for MySQL database, for example, `url ='jdbc:mysql://databaseName****-cn-shenzhen-a.ads.aliyuncs.com:10014/databaseName'`. The following descriptions about the parameters in the URL are provided:-   Perform the following steps to query the URI:
    1.  Log on to the [AnalyticDB for MySQL console](https://ads.console.aliyun.com/?spm=a2c4g.11186623.2.23.2c952b809T8asM).
    2.  Click the name of the cluster that you want to access to go to the **Basic Information** page.
    3.  In the **Connection Information** section, view the URL.
-   The databaseName parameter specifies the name of the AnalyticDB for MySQL database or the name of the AnalyticDB for MySQL instance. |
|tableName|The name of the table in the database|None.|
|username|The username that is used to log on to the database.|None.|
|password|The password that is used to log on to the database|None.|
|maxRetryTimes|The maximum number of retries for writing data to the table.|Optional. Default value: 10.|
|bufferSize|The maximum number of data records that can be stored in the buffer before data deduplication is triggered.|Optional. Default value: 5000. This value indicates that data deduplication is triggered if the number of input data records in the buffer reaches 5,000.|
|batchSize|The number of data records that are written at a time.|Optional. Default value: 1000.**Note:** If error code 20015 is returned, the batchSize parameter is set to a large value. For AnalyticDB for MySQL databases, the size of data records that are written at a time cannot exceed 1 MB. If the batchSize parameter is set to `1000`, the average size of each data record cannot exceed 1 KB. |
|batchWriteTimeoutMs|The timeout period for writing data.|Optional. Default value: 5000. Unit: milliseconds. This value indicates that if the number of input data records does not reach the value specified by the batchSize parameter within 5,000 milliseconds, all cached data is written to the result table.|
|connectionMaxActive|The maximum number of connections in a connection pool.|Optional. Default value: 30.|
|ignoreDelete|Specifies whether to skip the delete operation.|Default value: false.|

## Field type mapping

We recommend that you declare the mapping between the field data types of AnalyticDB for MySQL and Realtime Compute for Apache Flink in DDL statements.

|Data type of AnalyticDB for MySQL|Data type of Realtime Compute for Apache Flink|
|---------------------------------|----------------------------------------------|
|BOOLEAN|BOOLEAN|
|TINYINT|INT|
|SAMLLINT|
|INT|
|BIGINT|BIGINT|
|DOUBEL|DOUBLE|
|VARCHAR|VARCHAR|
|DATE|DATE|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|

