---
keyword: [create an Oracle database source table, Oracle database field, fail to query data in an Oracle database source table]
---

# Create an Oracle database source table

This topic describes how to create an Oracle database source table. It also describes the parameters in the WITH clause, data type mapping, sample code, and FAQ involved when you create an Oracle database source table.

**Note:**

-   This topic applies only to Blink 3.4.X and later.
-   You can create Oracle database source tables only when you use Oracle 11g.
-   You cannot change the number of concurrent jobs for an Oracle database source table. By default, only one job is allowed for a source table.

## Syntax

In Realtime Compute for Apache Flink, you can use an Oracle database to store input data. The following code shows an example:

```
create table oracle_source (
    EMPLOYEE_ID BIGINT,
    START_DATE TIMESTAMP,
    END_DATE TIMESTAMP,
    JOB_ID VARCHAR,
    DEPARTMENT_ID VARCHAR
) with (
    type = 'oracle',
    url = 'jdbc:oracle:thin:@//127.0.0.1:1521/ORACLE',
    userName = 'userName',
    password = 'password',
    dbName = 'hr',
    tableName = 'job_history',
    timeField = 'START_DATE',
    startTime = '2007-1-1 00:00:00'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the source table.|Yes|Set the value to oracle.|
|url|The connection string of the database.|Yes|The value of this parameter is in the `jdbc:oracle:thin:@//Database IP address:Port number/Database name` format. Example: jdbc:oracle:thin:@//127.0.0.1:1521/XE.|
|userName|The username that is used to log on to the database.|Yes|None.|
|password|The password that is used to log on to the database.|Yes|None.|
|tableName|The name of the table in the database. Database table names can be in one of the following formats: -   Table name 1,Table name 2
-   Database name.Table name 1,Table name 2

**Note:** Multiple table names are separated by commas \(,\).

|Yes|-   table1,table2
-   db1.table1,table2 |
|timeField|The time when the database was updated.|Yes|None.|
|dbName|The database name.|No|If you have specified the tableName parameter, you do not need to specify the dbName parameter.|
|startTime|The start time of reading data from the source table.|No|2019-5-15 00:00:00|
|timeZone|The time zone of the database.|No|Asia/Shanghai", "UTC|
|queryTimeRangeMs|The time that is taken for data retrieval. Unit: milliseconds. **Note:** The value of the queryTimeRangeMs parameter must be greater than the value of the queryIntervalMs parameter.

|No|Default value: 5000.|
|queryIntervalMs|The interval at which data is queried from the database. Unit: milliseconds.|No|Default value: 100.|
|connectionMaxActive|The maximum number of active connections.|No|Default value: 10.|
|maxRetry|The maximum number of retries upon a connection failure.|No|Default value: 3.|
|escapeFields|Specifies whether to escape field names in the database.|No|Valid values:-   false: not case-sensitive. This is the default value.
-   true: case-sensitive. |
|lengthCheck|The policy for checking the number of fields that are parsed from a row of data.|No|Valid values:-   NONE: This is the default value.
    -   If the number of fields parsed from a row of data is greater than the defined number of fields, data is extracted from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is less than the defined number of fields, the current row is skipped.
-   SKIP: If the number of fields parsed from a row of data is different from the defined number of fields, the current row is skipped.
-   EXCEPTION: If the number of fields parsed from a row of data is different from the defined number of fields, an error is returned.
-   PAD:
    -   If the number of fields parsed from a row of data is greater than the defined number of fields, data is padded from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is less than the defined number of fields, the values of the missing fields are padded with null from left to right. |
|columnErrorDebug|Specifies whether the debugging feature is enabled. **Note:** If the debugging feature is enabled, the system displays the logs that record the parse failures.

|No|Default value: false.|

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

The following example shows how to create an Oracle database source table in a Realtime Compute for Apache Flink job.

```
create table oracle_source (
    EMPLOYEE_ID BIGINT,
    START_DATE TIMESTAMP,
    END_DATE TIMESTAMP,
    JOB_ID VARCHAR,
    DEPARTMENT_ID VARCHAR
) with (
    type = 'oracle',
    url = 'jdbc:oracle:thin:@//127.0.0.1:1521/ORACLE',
    userName = 'userName',
    password = 'password',
    dbName = 'hr',
    tableName = 'job_history',
    timeField = 'START_DATE',
    startTime = '2007-1-1 00:00:00'
);

create table test_out(
    EMPLOYEE_ID BIGINT,
    START_DATE TIMESTAMP,
    END_DATE TIMESTAMP,
    JOB_ID VARCHAR,
    DEPARTMENT_ID VARCHAR
) with (
    type='print'
);

INSERT INTO test_out
SELECT 
    EMPLOYEE_ID,
    START_DATE,
    END_DATE,
    JOB_ID,
    DEPARTMENT_ID
from oracle_source;
```

## FAQ

Q: What do I do if no data is found?

A: The data cannot be found because Blink is faulty. To check whether Blink is faulty, view the `Round start:[{}], end:[{}]` and `Round read records` logs on the TaskManager tab. If the logs do not contain data, Blink is faulty.

**Note:**

-   `Round start:[{}], end:[{}]`: displays the start time of the queried data.
-   `Round read records`: displays the queried data records.

