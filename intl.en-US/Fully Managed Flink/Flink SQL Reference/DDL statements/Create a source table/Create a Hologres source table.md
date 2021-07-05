---
keyword: create a Hologres source table
---

# Create a Hologres source table

This topic describes how to create a Hologres source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create a Hologres source table.

## Introduction to Hologres

Hologres is compatible with the PostgreSQL protocol and closely connected to the big data ecosystem. Hologres allows you to analyze and process petabytes of data in high concurrency and low latency scenarios. Hologres provides an easy method for you to use the existing business intelligence \(BI\) tools to perform multidimensional analysis and explore your business.

## DDL syntax

```
create table hologres_source(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourAccessID>',
  'password'='<yourAccessSecret>',
  'endpoint'='<yourEndpoint>',
  'field_delimiter'='|' -- This parameter is optional.
);
```

**Note:**

-   Flink does not allow you to define computed columns in source tables.
-   Flink performs a full table scan once only to read all data from a Hologres source table at a time. Data consumption is complete when data scanning ends. Flink does not read the data that is appended to the Hologres source table.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the source table.|Yes|Set the value to hologres.|
|dbname|The name of the database.|Yes|None.|
|tablename|The name of the table.|Yes|None.|
|username|The username that is used to log on to the database. You must enter the AccessKey ID of your Alibaba Cloud account.|Yes|None.|
|password|The password that is used to log on to the database. You must enter the AccessKey secret of your Alibaba Cloud account.|Yes|None.|
|endpoint|The endpoint of Hologres.|Yes|For more information, see [Endpoints for connecting to Hologres](/intl.en-US/Manage Instances/Endpoints for connecting to Hologres.md).|
|field\_delimiter|The delimiter used between rows when data is being exported.**Note:** This parameter is valid only when bulkread is set to true.

|No|Default value: \\u0002.|

## Sample code

```
CREATE TEMPORARY TABLE hologres_source (
  name varchar, 
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourAccessID>',
  'password'='<yourAccessSecret>',
  'endpoint'='<yourEndpoint>',
  'field_delimiter'='|' -- This parameter is optional.
);

CREATE TEMPORARY TABLE blackhole_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT 
) with (
  'connector'='blackhole'
);

INSERT INTO blackhole_sink
SELECT 
   name, age, birthday
from hologres_source;
```

## Field type mapping

The following table lists the data type mappings between Hologres and Flink fields. We recommend that you declare the mappings in a DDL statement.

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

