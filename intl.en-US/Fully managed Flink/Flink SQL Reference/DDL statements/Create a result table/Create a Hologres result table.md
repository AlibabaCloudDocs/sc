---
keyword: create a Hologres result table
---

# Create a Hologres result table

This topic describes how to create a Hologres result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, streaming semantics, and field type mappings used when you create a Hologres result table.

## Introduction to Hologres

Hologres is compatible with the PostgreSQL protocol and closely connected to the big data ecosystem. Hologres allows you to analyze and process petabytes of data in high concurrency and low latency scenarios. Hologres provides an easy method for you to use the existing business intelligence \(BI\) tools to perform multidimensional analysis and explore your business.

## DDL syntax

```
create table hologres_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourUsername>',
  'password'='<yourPassword>',
  'endpoint'='<yourEndpoint>',
  'field_delimiter'='|' -- This parameter is optional.
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to hologres.|
|dbname|The name of the database.|Yes|None.|
|tablename|The name of the table.|Yes|None.|
|username|The username that is used to access the database.|Yes|None.|
|password|The password that is used to access the database.|Yes|None.|
|endpoint|The endpoint of Hologres.|Yes|Format: <ip\>:<port\>.|
|field\_delimiter|The field delimiter. Hologres streaming sink can split a string into arrays based on the value of this parameter and import the arrays into Hologres.|No|Default value: \\u0002.|
|mutateType|The data writing mode. For more information, see [Streaming semantics](#section_yce_507_nhr).|No|Default value: insertorignore.|
|partitionrouter|Specifies whether to write data to a partitioned table.|No|Default value: false.|
|ignoredelete|Specifies whether to ignore retraction messages.|No|Default value: false.**Note:** This parameter takes effect only when the streaming semantics is used. |
|createPartTable|Specifies whether to automatically create a non-existent partitioned table to which data is written based on partition values.**Note:** If the partition values contain hyphens \(-\), partitioned tables cannot be automatically created.

|No|-   false: Partitioned tables cannot be automatically created. This is the default value.
-   true: Partitioned tables can be automatically created.

**Note:**

-   VVR versions later than 2.1 support this parameter.
-   You must make sure that partition values do not contain dirty data. If dirty data exists, a failover occurs because an invalid partitioned table is created. Use this parameter with caution. |

## Streaming semantics

Stream processing, also known as streaming data or event processing, involves the processing of a series of unbounded data or events. A system that processes streaming data or events usually allows you to specify a reliability mode or processing semantics to ensure data accuracy because network or device faults may cause data loss.

Based on the configurations of Hologres streaming sink and Hologres table attributes, the following two types of semantics are supported:

-   Exactly-once: The system processes data or events only once even multiple faults occur.
-   At-least-once: If data or an event is lost before stream processing is complete, the system transfers all data and events again. In this case, data or events may be processed multiple times. If the first retry succeeds, no further retries are required.

When you use streaming semantics in a Hologres result table, take note of the following items:

-   If no primary keys have been configured in the Hologres physical table, Hologres streaming sink uses the at-least-once semantics.
-   If primary keys have been configured in the Hologres physical table, Hologres streaming sink uses the exactly-once semantics based on the primary keys. If multiple records with the same primary key are written to the table, you must set the mutationType parameter to determine how the result table is updated. This parameter has the following valid values:

    -   insertorignore: keeps the first record and discards the subsequent records. This is the default value.
    -   insertorreplace: replaces the existing data records in an entire row.
    -   insertorupdate: updates the existing data in specified fields. Assume that a table has four fields a, b, c, and d. The a field is the primary key. Only data in the a and b fields are written to Hologres. If duplicate primary keys exist, the system updates data only in the b field. Data in the c and d fields remains unchanged.
    **Note:**

    -   If the mutationType parameter is set to insertorupdate or insertorreplace, the system updates the data based on the primary key.
    -   The number of columns in the result table defined by Flink can be different from the number of columns in the Hologres physical table. Make sure that the value Null can be used to fill the missing columns. Otherwise, an error is returned.
-   By default, Hologres streaming sink can import data to only one non-partitioned table. If the sink imports data to the parent table of a partitioned table, data queries fail even if data import succeeds. To enable data to be automatically written to a partitioned table, you can set the partitionRouter parameter to true. Take note of the following items:
    -   You must set tablename to the name of the parent table.
    -   Connectors do not automatically create partitioned tables. We recommend that you create a partitioned table before you import data. Otherwise, the data fails to be imported.

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

