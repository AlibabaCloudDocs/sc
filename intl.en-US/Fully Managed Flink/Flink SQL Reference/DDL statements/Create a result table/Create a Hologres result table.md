---
keyword: create a Hologres result table
---

# Create a Hologres result table

This topic describes data definition language \(DDL\) statements, parameters in the WITH clause, streaming semantics, and field type mappings used when you create a Hologres result table.

**Note:**

-   The Hologres connector does not allow you to write result data to Hologres external tables. For more information about Hologres external tables, see [Manage a foreign table](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/MaxCompute Acceleration/Manage a foreign table.md).
-   The Hologres connector can be used to store result tables of streaming jobs and batch jobs.

## Introduction to Hologres

[Hologres](/intl.en-US/Product Introduction/What is Hologres?.md) is a real-time interactive analytics service that is developed by Alibaba Cloud. It is compatible with the PostgreSQL protocol and closely connected to the big data ecosystem. Hologres allows you to analyze and process petabytes of data with high concurrency and low latency. Hologres provides an easy method for you to use the existing business intelligence \(BI\) tools to perform multidimensional analysis and explore your business.

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
|dbname|The name of the database.|Yes|None|
|tablename|The table name.**Note:** If the schema is not public, you must set tablename to schema.tablename.

|Yes|None.|
|username|The username that is used to access the database.|Yes|None.|
|password|The password.|Yes|None.|
|endpoint|The endpoint of Hologres.|Yes|Format: <ip\>:<port\>.|
|field\_delimiter|The field delimiter. The Hologres streaming sink can split a string into arrays based on the value of this parameter and import the arrays into Hologres.|No|Default value: "\\u0002".|
|mutatetype|The data writing mode. For more information, see [Streaming data semantics](#section_yce_507_nhr).|No|Default value: insertorignore.|
|partitionrouter|Specifies whether to write data to a partitioned table.|No|Default value: false.|
|ignoredelete|Specifies whether to ignore retraction messages.|No|Default value: false. **Note:** This parameter takes effect only when the streaming data semantics is used. |
|createparttable|Specifies whether to automatically create a non-existent partitioned table to which data is written based on partition values. **Note:** If the partition values contain hyphens \(-\), partitioned tables cannot be automatically created.

|No|-   false: Partitioned tables cannot be automatically created. This is the default value.
-   true: Partitioned tables can be automatically created.

**Note:**

-   Ververica Runtime \(VVR\) later than 2.1 supports this parameter.
-   You must make sure that partition values do not contain dirty data. If dirty data exists, a failover occurs because an invalid partitioned table is created. Use this parameter with caution. |
|useRpcMode|Ververica Platform \(VVP\) 2.4.0 and later connect to Hologres by using a Java Database Connectivity \(JDBC\) driver. VVP earlier than 2.4.0 connects to Hologres by using Remote Procedure Call \(RPC\).|No|JDBC drivers require SQL connections and increase the number of JDBC links. If you need to decrease SQL connections, you can set this parameter to true. **Note:** Only VVP 2.4.0 and later support this parameter. |
|connectionSize|The size of the JDBC connection pool created by a Flink job. If the job has poor performance, we recommend that you increase the size of the connection pool. The connection pool size is proportional to data throughput.

|No|Default value: 3. **Note:** Only VVP 2.4.0 and later support this parameter. |
|jdbcWriteBatchSize|The maximum number of data rows that a Hologres streaming sink node can write into Hologres in a batch when a JDBC driver is used.|No|Default value: 256. Unit: data rows. **Note:** Only VVP 2.4.0 and later support this parameter. |
|jdbcWriteFlushInterval|The maximum period to wait for a Hologres streaming sink node to write data rows into Hologres in batches when a JDBC driver is used.|No|Default value: 10000. Unit: milliseconds. **Note:** Only VVP 2.4.0 and later versions support this parameter. |

## Streaming data semantics

Stream processing, also known as streaming data or event processing, refers to continuous processing of a series of unbounded data or events. In most cases, the system that processes streaming data or events allows you to specify a reliability pattern or processing semantics to ensure data accuracy. Network or device failures may cause data loss.

Semantics can be classified into the following types based on the configurations of the Hologres streaming sink that you use and the attributes of the Hologres table:

-   Exactly-once: The system processes data or events only once even multiple faults occur.
-   At-least-once: If streaming data or events to be processed are lost, the system transfers the data again from the transmission source. Therefore, the system may process streaming data or events for multiple times. If the first retry succeeds, no further retries are required.

When you use streaming semantics in a Hologres result table, take note of the following items:

-   If no primary keys have been configured in the Hologres physical table, the Hologres streaming sink uses the at-least-once semantics.
-   If primary keys have been configured in the Hologres physical table, the Hologres streaming sink uses the exactly-once semantics based on the primary keys. If multiple records with the same primary key are written to the table, you must set the mutatetype parameter to determine how the result table is updated. This parameter has the following valid values:

    -   insertorignore: retains the first record and discards the subsequent records. This is the default value.
    -   insertorreplace: replaces existing data records in an entire row.
    -   insertorupdate: updates existing data in specified fields. For example, a table has four fields a, b, c, and d. The a field is the primary key. Only data in the a and b fields are written to Hologres. If duplicate primary keys exist, the system updates data only in the b field. Data in the c and d fields remains unchanged.
    **Note:**

    -   If the mutatetype parameter is set to insertorupdate or insertorreplace, the system updates data based on the primary key.
    -   The number of columns in the result table defined by Flink can be different from the number of columns in the Hologres physical table. Make sure that the value Null can be used to pad the missing columns. Otherwise, an error is returned.
-   By default, the Hologres streaming sink can import data to only one non-partitioned table. If the sink imports data to the parent table of a partitioned table, data queries fail even if data import succeeds. To enable data to be automatically written to a partitioned table, you can set the partitionRouter parameter to true. Take note of the following items:
    -   You must set tablename to the name of the parent table.
    -   Connectors do not automatically create partitioned tables. We recommend that you create a partitioned table before you import data. Otherwise, the data fails to be imported.

## Merge data into a wide table

If you need to write data from multiple streaming jobs to one Hologres wide table, you can merge the data into a wide table. For example,

one Flink data stream contains A, B, and C fields, the other contains A, D, and E fields. The Hologres wide table WIDE\_TABLE contains A, B, C, D, and E fields, among which A field is the primary key. You can perform the following operations:

1.  Execute Flink SQL statements to create two Hologres result tables. One table is used to declare the A, B, and C fields, and the other is used to declare the A, D, and E fields. Map the two result tables to the Hologres wide table WIDE\_TABLE.
2.  Parameters settings of the two Hologres result tables:
    -   Set mutatetype to insertorupdate, which indicates that data is updated based on the primary key.
    -   Set ignoredelete to true, which prevents retraction messages from generating Delete requests.
3.  Insert data from the two Flink streams into the two result tables.

**Note:** Limits:

-   The wide table must have a primary key.
-   The data of each stream must contain all the fields in the primary key.
-   If the wide table is a column-oriented table and the requests per second \(RPS\) value is large, the CPU utilization increases. We recommend that you disable dictionary encoding for the fields in the table.

## Mapping between field data types

|Field type of Hologres|Field type of Realtime Compute for Apache Flink|
|----------------------|-----------------------------------------------|
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

