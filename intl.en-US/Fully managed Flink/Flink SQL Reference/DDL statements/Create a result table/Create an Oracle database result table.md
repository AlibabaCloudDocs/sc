---
keyword: create an Oracle database result table
---

# Create an Oracle database result table

This topic describes how to create an Oracle database result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create an Oracle database result table.

## Precautions

-   Flink concatenates each row of result data into a line of SQL statement and writes them to the destination database for execution.
-   Flink performs the following operations based on whether the required table exists in the Oracle database:
    -   If the required table exists, Flink writes or updates data to the result table.
    -   If the required table does not exist, Flink creates a table in the Oracle database to store the result data.
-   The primary keys of the logical and physical tables may not be the same. The primary key of the logical table must contain the primary key of the physical table. Otherwise, a primary key uniqueness error is returned when you insert result data into the result table.
-   The APPEND or UPSERT function is used to insert data into an Oracle database result table.
    -   If no primary key is specified in the table, the APPEND function is used.
    -   If a primary key is specified in the table, the UPSERT function is used. If no primary key exists, a primary key is inserted. If a primary key exists, the primary key is updated.

## DDL syntax

```
CREATE TABLE oracle_sink (
  employee_id BIGINT,
  phone_number VARCHAR,
  employee_age INT,
  PRIMARY KEY(employee_id)
) WITH (
  'connector' = 'oracle',
  'url' = '<yourUrl>',
  'userName' = '<yourUserName>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTableName>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to oracle.|
|url|The Java Database Connectivity \(JDBC\) URL of the Oracle database.|Yes|`jdbc:oracle:thin:@192.168.171.62:1521:sit0`|
|userName|The username that is used to access the database.|Yes|None.|
|password|The password that is used to access the database.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|maxRetryTimes|The maximum number of attempts to insert data into the result table.|No|Default value: 10.|
|batchSize|The number of data records that can be written at a time.**Note:**

-   The batchSize parameter takes effect only after the primary key is specified.
-   Write operations are triggered if the value of the batchSize or bufferSize parameter reaches the specified threshold.

|No|Default value: 50.|
|bufferSize|The maximum number of data records that can be stored in the buffer before data deduplication is triggered.**Note:**

-   The batchSize parameter takes effect only after the primary key is specified.
-   Write operations are triggered if the value of the batchSize or bufferSize parameter reaches the specified threshold.

|No|Default value: 500.|
|flushIntervalMs|The write timeout period. Unit: milliseconds. **Note:** If no data is written to the database within the period that is specified by this parameter, the system writes the cached data to the result table again.

|No|Default value: 500.|
|excludeUpdateColumns|Specifies whether to ignore the updates of the specified field.|No|This parameter is empty by default. This indicates that the data of the primary key is not updated when data in a row of the table is updated.|
|ignoreDelete|Specifies whether to ignore DELETE operations.|No|Valid values:-   false: DELETE operations are not ignored. This is the default value.
-   true: DELETE operations are ignored. |

## Field type mapping

|Data type of Oracle database|Data type of Flink|
|----------------------------|------------------|
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

CREATE TEMPORARY TABLE oracle_sink (
  employee_id BIGINT,
  employee_name VARCHAR,
  employ_age INT,
  primary key(employee_id)
) WITH (
  'connector' = 'oracle',
  'url' = '<yourUrl>',
  'userName' = '<yourUserName>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTableName>'
);

INSERT INTO oracle_sink SELECT * FROM oracle_source;
```

