---
keyword: [create an Oracle database result table, precautions for writing data to an Oracle database result table]
---

# Create an Oracle database result table

This topic describes how to create an Oracle database result table. It also describes the parameters in the WITH clause, data type mapping, and precautions.

**Note:**

-   This topic applies only to Blink 3.6.0 and later.
-   You can create Oracle database result tables only when you use Oracle 19c.

## Precautions

-   In Realtime Compute for Apache Flink, an SQL statement is executed to write each row of result data to the result table in the destination database.
-   Realtime Compute for Apache Flink performs the following operations based on whether the required table exists in the Oracle database:
    -   If the required table does not exist, Realtime Compute for Apache Flink creates a table in the Oracle database to store the result data.
    -   If the required table exists, Realtime Compute for Apache Flink writes or updates data to the result table.
-   The primary keys of logical and physical tables can be different. The primary keys of logical tables must contain the primary keys of physical tables.
-   Realtime Compute for Apache Flink uses the APPEND function or the UPSERT function to insert data into an Oracle database result table.
    -   If no primary key is specified in the table, the APPEND function is used.
    -   If a primary key is specified in the table, the UPSERT function is used. If the primary key does not exist, the primary key is inserted. If the primary key exists, the primary key is updated.

## Syntax

In Realtime Compute for Apache Flink, you can use an Oracle database to store output data. The following code shows an example:

```
CREATE TABLE oracle_sink(
  employee_id BIGINT,
  employee_name VARCHAR,
  employee_age INT,
  PRIMARY KEY(employee_id)
) WITH (
    type = 'oracle',
    url = '<yourUrl>',
    userName = '<yourUserName>',
    password = '<yourPassword>',
    tableName = '<yourTableName>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the result table.|Yes|Set the value to oracle.|
|url|The connection string of the database.|Yes|`jdbc:oracle:thin:@192.168.171.62:1521:sit0`|
|userName|The username that is used to log on to the database.|Yes|None.|
|password|The password that is used to log on to the database.|Yes|None.|
|tableName|The name of the table in the database.|Yes|None.|
|maxRetryTimes|The maximum number of retries for writing data to a table.|No|Default value: 10.|
|batchSize|The number of data records that are written at a time. **Note:**

-   The batchSize parameter takes effect only when the primary key is defined.
-   Write operations are triggered if the value of the batchSize or bufferSize parameter reaches the specified threshold.

|No|Default value: 50.|
|bufferSize|The number of data records in the buffer after duplicates are removed. **Note:**

-   The batchSize parameter takes effect only when the primary key is defined.
-   Write operations are triggered if the value of the batchSize or bufferSize parameter reaches the specified threshold.

|No|Default value: 500.|
|flushIntervalMs|The write timeout period. Unit: milliseconds. **Note:** If no data is written to the database within the period specified by this parameter, the system writes the cached data to the result table again.

|No|Default value: 500.|
|excludeUpdateColumns|Specifies whether to skip the specified columns when the data of a row in the table is updated. **Note:** When the data of a row in the table is updated, the primary key is not updated by default.

|No|This parameter is empty by default.|
|ignoreDelete|Specifies whether to skip delete operations.|No|Default value: false.|

## Mapping between field data types

|Data type of the Oracle database|Data type of Realtime Compute for Apache Flink|
|--------------------------------|----------------------------------------------|
|-   CHAR
-   VARCHAR
-   VARCHAR2

|VARCHAR|
|FLOAT|DOUBLE|
|NUMBER|BIGINT|
|DECIMAL|DECIMAL|

## Sample code

The following sample code shows how to create an Oracle database result table in a Realtime Compute for Apache Flink job.

```
CREATE TABLE oracle_source (
  employee_id BIGINT,
  employee_name VARCHAR,
  employ_age INT
) WITH (
  type = 'random'
);

CREATE TABLE oracle_sink(
  employee_id BIGINT,
  employee_name VARCHAR,
  employ_age INT,
  primary key(employee_id)
)with(
  type = 'oracle',
  url = 'jdbc:oracle:thin:@192.168.171.62:1521:sit0',
  userName = 'blink_test',
  password = 'blink_test',
  tableName = 'oracle_sink'
);

INSERT INTO oracle_sink
SELECT * FROM oracle_source;
```

