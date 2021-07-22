---
keyword: [Log Service, source table]
---

# Create a Log Service source table

This topic describes how to create a Log Service source table. It also describes data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, attribute fields, and sample code used when you create a Log Service source table.

## Introduction to Log Service

[Log Service](/intl.en-US/Product Introduction/What is Log Service?.md) is an end-to-end logging service. Log Service allows you to collect, consume, ship, query, and analyze log data in a quick manner. It improves the operations and maintenance \(O&M\) efficiency, and provides the capability to process large amounts of data.

## Prerequisites

A Log Service project and a Logstore are created. For more information, see [Step 2: Create a project and a Logstore](/intl.en-US/.md).

## DDL syntax

```
create table sls_source(
  a int,
  b int,
  c varchar
) with (
  'connector' = 'sls',  
  'endPoint' = '<yourEndPoint>',
  'accessId' = '<yourAccessId>',
  'accessKey' = '<yourAccessKey>',
  'startTime' = '<yourStartTime>',
  'project' = '<yourProjectName>',
  'logStore' = '<yourLogStoreName>',
  'consumerGroup' = '<yourConsumerGroupName>'
);
```

**Note:**

-   Log Service does not support the MAP data type.
-   Log Service sets the fields that do not exist to null.
-   Fields in the DDL statements can be unordered. We recommend that you place the fields in the DDL statements in the same order as those in physical tables.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the source table.|Yes|Set the value to `sls`.|
|endPoint|The consumer endpoint.|Yes|[Endpoints](/intl.en-US/Developer Guide/API Reference/Endpoints.md).|
|accessId|The AccessKey ID of your Alibaba Cloud account.|Yes|None.|
|accessKey|The AccessKey secret of your Alibaba Cloud account.|Yes|None.|
|project|The name of the Log Service project.|Yes|None.|
|logStore|The name of the Logstore.|Yes|None.|
|startTime|The time at which the logs start to be consumed.|Yes|None.|
|consumerGroup|The name of the consumer group.|No|You can specify this parameter as required. The format of the name is not fixed.|
|batchGetSize|The number of log items that can be read from a log group at a time.|No|Default value: 100. **Note:**

-   The value of the batchGetSize parameter cannot exceed 1,000. Otherwise, an error is returned.
-   This parameter specifies the number of log items that can be read from a log group at a time. If both the size of a log item and the value of the batchGetSize parameter are large, frequent garbage collections may occur. To prevent this issue, you can set the batchGetSize parameter to a small value. |

## Mapping between field data types

The following table describes the data type mappings between Log Service and Flink fields. We recommend that you declare the mappings in a DDL statement.

|Data type of Log Service|Data type of Flink|
|------------------------|------------------|
|STRING|VARCHAR|

## Attribute fields

|Field|Type|Description|
|-----|----|-----------|
|`__source__`|STRING METADATA VIRTUAL|The message source.|
|`__topic__`|STRING METADATA VIRTUAL|The message topic.|
|`__timestamp__`|BIGINT METADATA VIRTUAL|The timestamp at which logs are generated.|

**Note:** Only VVR 3.0.1 and later can obtain the Log Service attribute fields in the preceding table.

## Sample code

```
create table sls_input(
  `time` bigint,
  `url` string,
  dt string,
  float_field float,
  double_field double,
  boolean_field boolean,
  `__topic__` STRING METADATA VIRTUAL,
  `__source__` STRING METADATA VIRTUAL,
  `__timestamp__` BIGINT METADATA VIRTUAL,
  proctime as PROCTIME()
) with (
  'connector' = 'sls',
  'endpoint' = '<yourEndPoint>',
  'accessid' = '<yourAccessId>',
  'accesskey' = '<yourAccessKey>',
  'starttime' = '2001-08-01 00:00:00',
  'project' = 'sls-test',
  'logstore' = 'sls-input'
);
```

